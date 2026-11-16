# Eliminating False-Positive Mismatch Warnings in Dismech: Extracting Bare OMIM Phenotype Identifiers and Isolating Gene Mentions

## Overview

In the Monarch Initiative's open source bioinformatics repository [dismech](https://github.com/monarch-initiative/dismech), the automated quality assurance pipeline (`preflight-dr`) verifies curated deep-research disease reports against ontology records in the Mondo Disease Ontology (MONDO). The tool was generating false-positive warning diagnostics because its identifier extractor failed to recognize OMIM phenotype numbers written as bare hashes (such as `#619463` or `%310200`) or styled with markdown formatting (such as `OMIM: **535000**`), preventing valid reports from matching ontology cross-references. To resolve this, I overhauled the extraction logic in `src/dismech/preflight_dr.py` by introducing markdown normalization, regex lookaround guards, and domain-aware bucket separation to isolate phenotype entries from gene entries (`*`, `+`). I backed the fix with five new unit tests in `tests/test_preflight_dr.py` and authored an automated pre-ship verification manifest (`ship-manifest.md`) covering unit test suites and syntax compilation. My changes passed all 81 unit tests and verified cleanly under automated ship-gate checks. I opened [Pull Request #1 on my fork](https://github.com/sid-pandya/dismech/pull/1) with an upstream compare target to [monarch-initiative/dismech:main](https://github.com/monarch-initiative/dismech/compare/main...sid-pandya:dismech:fix/10458-bare-omim-phenotype-numbers), where it currently stands open and awaiting maintainer review.

## What happened

### 1. Investigating and Reproducing the Parser Failures

In `monarch-initiative/dismech`, disease reports generated through literature curation are evaluated prior to knowledge-base ingestion by `preflight-dr`. A critical identity check involves comparing Online Mendelian Inheritance in Man (OMIM) numbers cited in report prose against the MONDO disease term's known `omim_ids`. 

When analyzing [Issue #10458](https://github.com/monarch-initiative/dismech/issues/10458), I inspected `src/dismech/preflight_dr.py` and found that the extraction regular expression strictly required an explicit `OMIM` or `MIM` prefix:
```python
OMIM_RE = re.compile(r"\b(?:OMIM|MIM)\s*[:#\s]\s*#?\s*(\d{6})\b", re.IGNORECASE)
```
In medical genetics literature, authors frequently write phenotype entries as bare sigils (such as `#619463` or `%310200`). Furthermore, automated text formatters frequently introduce bold markdown or escape characters (such as `OMIM: **535000**` or `OMIM \*610008`). Because `OMIM_RE` failed on these patterns, `preflight-dr` found zero matching identifiers and flagged valid disease reports with false-positive `WARN` diagnostics.

To confirm the failure before modifying any code, I constructed a local reproduction script (`repro.py`) that evaluated candidate strings against the unpatched `extract_omim_ids()` function:

```text
=== RUNNING PRE-PATCH REPRODUCTION TESTS ===
[OMIM:619463              ] -> extracted: ['619463']
[MIM #605282              ] -> extracted: ['605282']
[#619463                  ] -> extracted: set()
[(#619463)                ] -> extracted: set()
[%310200                  ] -> extracted: set()
[OMIM: **535000**         ] -> extracted: set()
[OMIM \*610008            ] -> extracted: set()
[OMIM *300822             ] -> extracted: set()
[MIM number **619422**    ] -> extracted: set()
[whitespace after sigil   ] -> extracted: set()
[markdown list bullet     ] -> extracted: set()
[eight-digit PMID         ] -> extracted: set()
[CURIE local id           ] -> extracted: set()
```
The output proved that every bare identifier and markdown-emphasized pattern was completely dropped by the existing implementation.

### 2. A Change I Made: Domain-Aware Separation of Phenotypes and Genes

My initial thought was to simply broaden the regex to match any six-digit number preceded by `#`, `%`, `*`, or `+`. However, examining OMIM numbering conventions and MONDO database schemas revealed a critical domain rule that overturned that initial approach:
- OMIM entries prefixed with `#` (phenotype with known molecular basis) and `%` (phenotype with mapped locus) represent **phenotypes / diseases**.
- OMIM entries prefixed with `*` (gene with known sequence) and `+` (gene and phenotype combined) represent **genes**.

MONDO disease records only cross-reference phenotype MIM numbers (`record.omim_ids`). If gene MIM numbers were returned in `extract_omim_ids()`, any report citing a disease's causal gene MIM would introduce foreign numbers into the comparison set. Because those gene numbers would never match the disease record's phenotype xrefs, a naive fix would actually manufacture new false-positive `WARN` diagnostics!

Based on this discovery, I changed my design from a single catch-all extraction to a two-bucket architecture:
1. Stripping markdown formatting (`**`, `__`, `\`) before pattern evaluation via `EMPHASIS_RE`.
2. Extracting phenotype MIM numbers (`#`, `%`) in `extract_omim_ids()` with strict lookaround guards: `(?<![\w:])` to avoid matching inside ontology CURIEs (such as `MONDO:0030397`) and `(?!\d)` to prevent matching substrings of longer numbers (such as 8-digit PMIDs).
3. Strictly prohibiting whitespace between bare sigils and digits so negative controls like table percentages (`45% 123456`) or markdown list bullets (`+ 123456`) are rejected.
4. Routing gene MIM numbers (`*`, `+`) into a dedicated helper function, `extract_omim_gene_ids()`.
5. Updating `PreflightResult` and `assess()` to record `report_gene_omim` for informational reporting without letting gene numbers contaminate the disease identity pass/fail verdict.

Here is the implementation diff applied in `src/dismech/preflight_dr.py`:

```diff
+# Markdown emphasis (**, __) and backslash escapes sit between the prefix
+# and the number in DR prose: "OMIM: **535000**", r"OMIM \*610008". Normalize them first.
+EMPHASIS_RE = re.compile(r"\*\*|__|\\")
+
+# '#' and '%' key a phenotype entry; '*' and '+' key a gene entry; '^' is moved/removed.
+# MONDO xrefs phenotype MIMs, so only the phenotype set is comparable to record.omim_ids --
+# a gene MIM in that comparison set can only ever manufacture a false mismatch WARN.
+# Whitespace is strictly disallowed between a bare sigil and its digits to avoid false
+# positives on table percentages ("45% 123456") or markdown list bullets ("+ 123456").
+PHENOTYPE_SIGIL_RE = re.compile(r"(?<![\w:])[#%](\d{6})(?!\d)")
+GENE_SIGIL_RE = re.compile(r"(?<![\w:])[*+](\d{6})(?!\d)")
+
+# Prefixed OMIM / MIM identifiers as they appear in DR prose: "OMIM:616291", "OMIM 616291",
+# "MIM #605282", "(MIM 616354)", "OMIM: 535000", "MIM number 619422", "OMIM *300822".
+OMIM_PREFIXED_RE = re.compile(
+    r"\b(?:OMIM|MIM)(?:\s+(?:number|id|entry|no\.?))?\s*[:#\s]?\s*([#%*+]?)\s*(\d{6})(?!\d)",
+    re.IGNORECASE,
+)
+OMIM_RE = OMIM_PREFIXED_RE
```

```diff
-def extract_omim_ids(text: str) -> set[str]:
-    """Return the six-digit OMIM/MIM identifiers cited in ``text``."""
-    return set(OMIM_RE.findall(text))
+def extract_omim_ids(text: str, *, include_genes: bool = False) -> set[str]:
+    """Return the six-digit OMIM/MIM identifiers cited in ``text``.
+
+    By default, returns phenotype MIM numbers (bare `#`, `%`, or prefixed
+    without gene sigils) suitable for comparing against MONDO disease xrefs.
+    Pass ``include_genes=True`` to include gene MIM numbers (`*`, `+`).
+    """
+    cleaned = EMPHASIS_RE.sub("", text)
+    phenotypes = set(PHENOTYPE_SIGIL_RE.findall(cleaned))
+    genes = set(GENE_SIGIL_RE.findall(cleaned))
+    for sigil, digits in OMIM_PREFIXED_RE.findall(cleaned):
+        if sigil in ("*", "+"):
+            genes.add(digits)
+        else:
+            phenotypes.add(digits)
+    if include_genes:
+        return phenotypes | genes
+    return phenotypes
+
+
+def extract_omim_gene_ids(text: str) -> set[str]:
+    """Return only gene OMIM identifiers (`*`, `+`) cited in ``text``."""
+    cleaned = EMPHASIS_RE.sub("", text)
+    genes = set(GENE_SIGIL_RE.findall(cleaned))
+    for sigil, digits in OMIM_PREFIXED_RE.findall(cleaned):
+        if sigil in ("*", "+"):
+            genes.add(digits)
+    return genes
```

### 3. Writing Unit Tests and Catching Edge Cases

To ensure long-term regression safety, I authored five comprehensive unit tests in `tests/test_preflight_dr.py` covering bare phenotype extraction, markdown emphasis stripping, gene bucket isolation, negative controls, and verdict preservation:

```python
def test_extract_omim_ids_gene_sigils_and_separation():
    """Gene sigils (* and +) are kept separate from phenotype MIMs (#10458)."""
    text = r"cites OMIM *300822 and OMIM \*610008 and bare *300821 and +123456"
    assert extract_omim_ids(text) == set()
    assert extract_omim_gene_ids(text) == {"300822", "610008", "300821", "123456"}
    assert extract_omim_ids(text, include_genes=True) == {"300822", "610008", "300821", "123456"}

def test_extract_omim_ids_negative_controls():
    """Negative controls must not produce false-positive OMIM IDs (#10458)."""
    assert extract_omim_ids("a 45% response, 123456 cells") == set()
    assert extract_omim_ids("+ 123456 items") == set()
    assert extract_omim_ids("PMID **37963421**") == set()
    assert extract_omim_ids("MONDO:0030397") == set()

def test_report_citing_only_gene_mims_does_not_downgrade_pass_to_warn():
    """Reports citing only gene MIMs do not trigger false mismatch WARNs (#10458)."""
    text = "CHSY1 is the causal gene. Mentions OMIM *300822 and *300824 in gene list."
    counts = Counter({"CHSY1": 15})
    phenotype_omim = extract_omim_ids(text)
    gene_omim = extract_omim_gene_ids(text)
    result = assess(REC_TEMTAMY_PREAXIAL, counts, phenotype_omim, report_gene_omim=gene_omim)
    assert result.verdict == PASS
    assert result.report_gene_omim == ["300822", "300824"]
    assert result.report_omim == []
```

I executed the test suite using `uv run pytest tests/test_preflight_dr.py -v`:
```text
======================== 81 passed, 2 skipped in 4.43s ========================
```
All 81 active component tests passed cleanly (the two skipped tests were integration suites requiring local ontology database builds).

### 4. Automated Gate Validation and Results That Landed

To verify branch readiness prior to submission, I created `dismech/ship-manifest.md` to define our required verification commands:
```markdown
| Check | Command | Source |
|---|---|---|
| Preflight unit tests | uv run pytest tests/test_preflight_dr.py | Issue #10458 instructions & CLAUDE.local.md |
| Python syntax compilation | python3 -m py_compile src/dismech/preflight_dr.py tests/test_preflight_dr.py | CLAUDE.local.md |
```

When I first ran the automated `ship-gate` verification script (`check_gate.py`), the gate stopped: my local `main` branch was lagging behind `upstream/main` by five commits, causing unrelated disorder curation commits to appear in the diff comparison. I fast-forwarded local `main` to `upstream/main` and re-executed `check_gate.py`.

The second run produced a clean result that landed:
```text
=== SHIP GATE CHECK ===
manifest-checks: PASS (Preflight unit tests: ok 4.4s, Python syntax compilation: ok 0.1s)
diff-matches-plan: PASS
description-claims-hold: PASS
evidence-is-observable: PASS
diff-is-reviewable: PASS
standards-met: PASS
title-is-specific: PASS
commits-are-scoped: PASS
OVERALL VERDICT: PASS
```

With gate verification confirmed, I committed the changes as commit `3a9114561b8` and pushed branch `fix/10458-bare-omim-phenotype-numbers` to my fork. 

When preparing upstream delivery, I checked `CONTRIBUTING.md` (lines 71-84), which instructs contributors not to open PRs from forks (which are closed automatically by repository automation) and instead to open an issue requesting branch access to `origin`. I ran `gh issue create` to request contributor access, but GitHub returned `GraphQL: sid-pandya does not have the correct permissions to execute CreateIssue` due to active repository interaction limits restricting issue creation to established contributors. I documented this dual repository and permission constraint, opened [Pull Request #1 on my fork](https://github.com/sid-pandya/dismech/pull/1) with complete passing test logs, and provided an upstream compare branch link for the maintainers.

## Limitations and what's next

While this solution resolves false-positive mismatch warnings for valid reports, it carries two notable technical limitations:

1. **Strict Whitespace Sensitivity for Bare Sigils:** `PHENOTYPE_SIGIL_RE` requires zero whitespace between the sigil and digits (e.g., `#619463`). This boundary is strictly necessary to prevent false matches against table percentages (`45% 123456`) or list bullets (`+ 123456`). However, if a human curator accidentally types `# 619463` with a space, the identifier will be missed. What I would build next is a contextual parser that allows whitespace only when the token is immediately preceded by keyword phrases such as `OMIM phenotype` or `MIM entry`.

2. **Syntactic Formatting vs. Semantic Identifier Validity:** The current regex validates six-digit numbers and boundary conditions, but does not verify whether an extracted number represents an active record in the live OMIM catalogue or an obsolete, retired entry. What I would implement next is an offline validation cache—such as a Bloom filter or set of active OMIM numbers compiled during build time—to verify that extracted integers represent valid disease entities before passing them to MONDO cross-reference checking.

Regarding pull request status, the fix is packaged as commit `3a9114561b8` on branch `fix/10458-bare-omim-phenotype-numbers` and is documented in [Pull Request #1 on my fork](https://github.com/sid-pandya/dismech/pull/1). Because upstream repository interaction limits currently restrict new contributors from opening issues on `monarch-initiative/dismech`, the PR awaits review and branch onboarding by the maintainers via the upstream compare branch link.
