# Eliminating False-Positive Mismatch Warnings in Dismech: Extracting Bare OMIM Phenotype Identifiers and Isolating Gene Mentions

## Overview

In the Monarch Initiative's open source bioinformatics repository [dismech](https://github.com/monarch-initiative/dismech), the automated quality assurance pipeline (`preflight-dr`) verifies curated deep-research disease reports against ontology records in the Mondo Disease Ontology (MONDO). The tool was generating false-positive warning diagnostics because its identifier extractor failed to recognize Online Mendelian Inheritance in Man (OMIM) phenotype numbers written as bare hashes (such as `#619463` or `%310200`) or styled with markdown formatting (such as `OMIM: **535000**`), preventing valid reports from matching ontology cross-references. Working from maintainer analysis on [Issue #10458](https://github.com/monarch-initiative/dismech/issues/10458), I implemented a robust extraction pipeline in `src/dismech/preflight_dr.py` that normalizes markdown emphasis, enforces regex lookaround boundaries, and separates phenotype entries from gene entries (`*`, `+`). I backed the fix with five new unit tests in `tests/test_preflight_dr.py` and authored an automated pre-ship verification manifest (`ship-manifest.md`) covering unit tests and syntax compilation. After resolving branch divergence that initially blocked our automated ship-gate, the fix achieved 81 passing unit tests and a clean gate pass. I packaged the solution in [Pull Request #1 on my fork](https://github.com/sid-pandya/dismech/pull/1) alongside an [upstream compare link to monarch-initiative/dismech:main](https://github.com/monarch-initiative/dismech/compare/main...sid-pandya:dismech:fix/10458-bare-omim-phenotype-numbers), providing a turn-key patch staged for maintainers while navigating upstream origin-branch permissions.

## What happened

### 1. Investigating and Reproducing the Parser Failures

In `monarch-initiative/dismech`, disease reports generated through literature curation are evaluated prior to knowledge-base ingestion by `preflight-dr`. A critical identity check involves comparing OMIM numbers cited in report prose against the MONDO disease term's known `omim_ids`.

When inspecting [Issue #10458](https://github.com/monarch-initiative/dismech/issues/10458), I examined `src/dismech/preflight_dr.py` and found that the extraction regular expression strictly required an explicit `OMIM` or `MIM` prefix followed by punctuation or whitespace:
```python
OMIM_RE = re.compile(r"\b(?:OMIM|MIM)\s*[:#\s]\s*#?\s*(\d{6})\b", re.IGNORECASE)
```
In medical genetics literature, authors frequently write phenotype entries as bare sigils (such as `#619463` or `%310200`). Furthermore, curation reports frequently introduce bold markdown or escape characters (such as `OMIM: **535000**` or `OMIM \*610008`). Because `OMIM_RE` failed on these patterns, `preflight-dr` extracted an empty set, failed ontology cross-referencing, and flagged valid disease reports with false-positive `WARN` diagnostics.

To verify the failure before touching production code, I created a local reproduction script (`repro.py`) evaluating candidate strings against the unpatched `extract_omim_ids()` function:

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
The reproduction output confirmed that unpatched code dropped all bare phenotype numbers and bolded prefix numbers.

### 2. Translating Maintainer Design into Production Code: Phenotypes vs. Genes

While an initial naive reaction to the issue might be to relax the regex to match any six-digit number preceded by `#`, `%`, `*`, or `+`, maintainer analysis on Issue #10458 pointed out a critical domain rule and regression hazard:

> "Scanning the committed corpus turns up a **second, larger miss class that this issue doesn't mention** — markdown emphasis sitting between the `OMIM` prefix and the digits — and a **regression hazard** in the proposed fix that's worth designing around before anyone writes the regex."

In the OMIM numbering system, `#` and `%` key phenotype entries, whereas `*` and `+` key gene entries. MONDO disease records only cross-reference phenotype MIM numbers (`record.omim_ids`). If gene MIMs were admitted into the comparison set, any report referencing a disease's causal gene MIM would introduce foreign numbers into the set intersection. Because those gene numbers would never match MONDO phenotype xrefs, a naive fix would actually manufacture new false-positive `WARN` diagnostics.

Following the maintainer's five-point architectural directive, I translated the design into code:
1. Stripping markdown formatting (`**`, `__`, `\`) before pattern evaluation via `EMPHASIS_RE`.
2. Extracting phenotype MIM numbers (`#`, `%`) in `extract_omim_ids()` with strict lookaround guards: `(?<![\w:])` avoiding CURIE prefixes (e.g. `MONDO:0030397`) and `(?!\d)` preventing partial matches on longer numbers (e.g. 8-digit PMIDs).
3. Strictly disallowing whitespace between bare sigils and digits so negative controls like table percentages (`45% 123456`) or markdown list bullets (`+ 123456`) are rejected.
4. Capturing gene MIM numbers (`*`, `+`) in a dedicated helper function, `extract_omim_gene_ids()`.
5. Preserving `OMIM_RE = OMIM_PREFIXED_RE` as an alias for backward compatibility.
6. Extending `PreflightResult` and `assess()` to record `report_gene_omim` for informational reporting without letting gene numbers contaminate the disease identity pass/fail verdict.

Here is the implementation diff in `src/dismech/preflight_dr.py`:

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

### 3. Authoring Unit Tests and Catching Edge Cases

To ensure thorough test coverage, I added five unit tests to `tests/test_preflight_dr.py` validating bare phenotype extraction, markdown emphasis stripping, gene bucket isolation, negative controls, and verdict preservation:

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

I ran the suite using `uv run pytest tests/test_preflight_dr.py -v`:
```text
======================== 81 passed, 2 skipped in 4.43s ========================
```
All 81 component tests passed cleanly (the two skipped tests were integration sweeps requiring local ontology database builds).

### 4. A Change I Made: Resolving Branch Drift and Passing Ship-Gate

To enforce disciplined shipping standards, I authored `dismech/ship-manifest.md` to define required verification gates:
```markdown
| Check | Command | Source |
|---|---|---|
| Preflight unit tests | uv run pytest tests/test_preflight_dr.py | Issue #10458 instructions & CLAUDE.local.md |
| Python syntax compilation | python3 -m py_compile src/dismech/preflight_dr.py tests/test_preflight_dr.py | CLAUDE.local.md |
```

When I first ran our automated verification tool (`check_gate.py`) at 19:34, the ship-gate blocked the commit:

```text
- verdict: BLOCK (block)
  - diff-matches-plan [fail]: Diff stat includes kb/disorders/Deeah_Syndrome.yaml (+384), Hypokalemic_Periodic_Paralysis.yaml (+636), Neurofibromatosis-Noonan_Syndrome.yaml (+1088), RUNX1_Familial_Platelet_Disorder.yaml (+718), Spermatogenic_Failure_23.yaml (+127), plus 10 history/ and 5 references_cache/ files, none named in plan.md's scope or disclosed anywhere.
  - commits-are-scoped [fail]: Commit list mixes the one relevant fix commit with five unrelated 'Rebase X onto main and address review feedback' commits
```

**What happened and what I changed:** My local `main` branch was lagging five commits behind `upstream/main`. As a result, the tool evaluated my branch against a stale base, pulling in five upstream disorder curation commits and 15 cache/history files. I fast-forwarded local `main` to `upstream/main`, rebased my fix cleanly onto the updated main, and re-ran `check_gate.py` at 19:38.

The second run produced the intended result that landed:

```text
- verdict: PASS
  - Preflight unit tests: `uv run pytest tests/test_preflight_dr.py` -> ok (7s)
  - diff-matches-plan [pass]: Both changed files match plan.md's steps and the maintainer comment's 5-point directive; no hunk outside src/dismech/preflight_dr.py or tests/test_preflight_dr.py.
  - commits-are-scoped [pass]: Single commit 3a9114561b8 bundles one single-shape fix with its own directly-coupled tests.
```

With gate checks passed, I committed the scoped changes as commit `3a9114561b8` and pushed branch `fix/10458-bare-omim-phenotype-numbers` to my fork.

When preparing upstream delivery, I checked `CONTRIBUTING.md` (lines 71-84), which specifies an origin-branch model: fork PRs are closed automatically by automation, and contributors must first open an issue requesting push access to `origin`. When I ran `gh issue create`, GitHub returned `GraphQL: sid-pandya does not have the correct permissions to execute CreateIssue` due to repository interaction limits restricting issue creation to established contributors. I opened [Pull Request #1 on my fork](https://github.com/sid-pandya/dismech/pull/1) with complete reproduction logs and provided the upstream compare link, creating a fully verified, reviewable patch ready for maintainers to pull once contributor access opens.

## Limitations and what's next

While this implementation resolves false-positive mismatch warnings for valid reports, it carries two specific technical limitations:

1. **Strict Whitespace Sensitivity for Bare Sigils:** `PHENOTYPE_SIGIL_RE` requires zero whitespace between the sigil and digits (e.g., `#619463`). This constraint is necessary to prevent false matches against table percentages (`45% 123456`) or list bullets (`+ 123456`). However, if a curator accidentally enters `# 619463` with a space, the identifier will be missed. What I would build next is a contextual lookaround parser that allows whitespace only when preceded by keywords like `OMIM phenotype` or `MIM entry`.

2. **Syntactic Formatting vs. Semantic Identifier Validity:** The regex validates six-digit numbers and boundary conditions, but does not verify whether an extracted number represents an active record in the live OMIM catalogue or an obsolete, retired entry. What I would build next is an offline validation cache—such as a Bloom filter or set of active OMIM numbers compiled during build time—to verify that extracted integers represent valid disease entities before passing them to MONDO cross-reference checking.

Regarding pull request status, the fix is packaged as commit `3a9114561b8` on branch `fix/10458-bare-omim-phenotype-numbers` and is documented in [Pull Request #1 on my fork](https://github.com/sid-pandya/dismech/pull/1). Because upstream repository interaction limits currently restrict new contributors from opening issues on `monarch-initiative/dismech`, the PR is staged on the fork with passing test evidence, ready for maintainer onboarding via the upstream compare branch link.
