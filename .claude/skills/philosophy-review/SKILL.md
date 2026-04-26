# Philosophy Review Skill

Audit a Mündig research doc against the five AI-assisted-research failure modes documented in [`docs/ai-assisted-research.md`](../../../docs/ai-assisted-research.md). Produce a concrete, actionable report. Do not fix issues unless explicitly asked — the audit is a finding, the fix is a separate decision.

## When to run

- After AI-drafted additions to any file in `docs/`.
- Before sharing a doc as part of an open-source release or as a response to a contribution.
- When a doc has gone untouched for a while and may have drifted from the project's standards.
- On request, against any specific file or glob.

## 1. Establish the target

- If the user passed a file path or glob, use it.
- If the IDE has a doc-looking file selected (`docs/**/*.md`), confirm with the user.
- Else ask which doc(s) to review. Do not guess.

## 2. Read the target end-to-end before flagging anything

A reviewer who reads in pieces will hallucinate problems and miss real ones. Read the whole file (or set of files) once, then go back through with the five lenses.

## 3. The five lenses

For each target file, check:

### Lens 1 — Citations (failure mode 3)
- Is every claim attributed to a thinker or text accompanied by **work + year**, ideally with a section/chapter reference?
- For direct quotes: are they in quote marks, with a specific source location?
- Is any famous misattribution present unchallenged? Watch for: Voltaire's "I disapprove of what you say…" (Hall's paraphrase), Smith's "invisible hand" misuses, Niemöller "first they came…" variants, Kant aphorisms attributed across his corpus, Einstein/Twain/Churchill quotation salads.
- Is any paraphrase presented as if it were a direct quote?
- Are uncertain attributions flagged (`[citation needed]`, "this attribution should be verified") rather than left as confident bare claims?

### Lens 2 — Smoothed-over disagreements (failure mode 1)
- Where the doc compares thinkers or summarizes a debate, is the genuine disagreement preserved, or has it been collapsed into a bland midpoint?
- Specific patterns to flag: "X and Y both argue…" when X and Y in fact disagree; treating Habermas vs. Fraser, Locke vs. Rousseau, or analytic vs. continental positions as if they were variations on a single theme.

### Lens 3 — Textbook reading by default (failure mode 2)
- Where the doc makes interpretive claims about a thinker, is it presenting the most-cited textbook reading?
- Is there a contested or live scholarly reading that should be acknowledged?
- If only the textbook reading is being deployed, is that limitation marked ("on the standard reading…", "the most-cited interpretation is…")?

### Lens 4 — Unmarked ideological priors (failure mode 4)
- Are interpretations presented as neutral that actually reflect a particular framing?
- Watch for: procedural-liberal vs. critical-theoretic readings of Habermas; libertarian vs. social-democratic readings of Locke and Smith; consensus vs. agonistic readings of the public sphere.
- Is the framing named as a choice, or smuggled in as the obvious reading?

### Lens 5 — Plausibility hiding low confidence (failure mode 5)
- Does the prose sound more confident than the underlying claim warrants?
- Are interpretive claims marked as interpretive, or written in the same declarative voice as well-sourced facts?
- Are uncertainties hedged with *specific* markers ("the standard reading is", "this is contested", "the section reference should be verified") rather than generic mush ("perhaps", "arguably")?

## 4. Output format

Produce a report grouped by file. For each file:

```
## <relative file path>

### Findings

- [line N] **<lens name>**: <specific issue>. Suggestion: <concrete fix>.
- [line N] **<lens name>**: ...
- ...

### Verdict

<one paragraph: does the file pass, or are there must-fix items? Quantify roughly — "clean", "minor", "needs work", "blocking">
```

Be concrete. *"The Habermas section is too smooth"* is useless. *"Line 47 presents Habermas's 1962 thesis without acknowledging Fraser's 1990 challenge already covered in critiques.md — add a back-reference or a one-line caveat"* is useful.

## 5. Don't

- **Don't fix issues yourself unless the user asks.** The audit is a finding; the fix is a separate decision. Often the user will want to weigh which findings to act on.
- **Don't pad findings to look thorough.** If a doc is clean against all five lenses, say so in one line and move on.
- **Don't audit prose style, argumentative quality, or design ideas.** The five failure modes are the scope. Out-of-scope feedback dilutes the signal.
- **Don't audit the design moves (`docs/techno-feudalism/habermas/deliberative-social-app/`) under lenses 2, 3, and 4** unless they make new philosophical claims. Most of that folder is applied design that back-references the framework files; the framework files are the right audit target.

## Scaling

- **Single file**: do the audit yourself, in this conversation.
- **Multiple files (3+)**: spawn one agent per file in parallel (single message, multiple Agent tool calls), each running this same procedure on its assigned file. Aggregate the reports. Pass the agent the file path AND a copy of the five lenses above so it doesn't have to re-read this skill file.
