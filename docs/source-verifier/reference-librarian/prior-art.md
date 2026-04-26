# Prior art — open-source citation-verification tooling

Survey of existing open-source citation-verification tooling, conducted 2026-04 ahead of building bibcheck (see [`README.md`](README.md) for context). The purpose of the survey was to **verify that bibcheck is not duplicating an existing tool** — and the answer turned out to be more interesting than a simple yes / no. This file is the persistent design memory of what was found, what bibcheck depends on, and what bibcheck deliberately does not replace.

The survey covered npm, PyPI, GitHub repository search, awesome-lists for academic / scholarly tooling, and the LLM-citation-verification web tools previously catalogued in [`../tooling-landscape.md`](../tooling-landscape.md).

> **Bottom line:** No tool fully overlaps. The existence-check core is commodity — adequately covered by 5+ active 2025–2026 tools — and bibcheck explicitly does not compete there. The genuine gaps bibcheck fills are the canonical-edition URL whitelist, the deterministic Pandoc/Markdown linkage check, the structured human-triage worklist, and SARIF output for CI. bibcheck additionally exposes a generic project-supplied phrase-denylist subcommand (`bibcheck phrases`) — opt-in, no curated baseline — which is a generic lint surface no surveyed tool provides for markdown/prose with file:line anchors and acknowledgement markers. The canonical-edition whitelist is the strongest differentiator and has **no existing implementation** in the surveyed corpus.

## Active tools we depend on

Libraries and tools `bibcheck` depends on rather than re-implementing.

### `citation-js` / `@citation-js/cli`

- Repo: <https://github.com/citation-js/citation-js>
- Status: Active, last release 2025-12.
- Language: JavaScript / TypeScript.
- What it does: parses and converts between CSL JSON, BibTeX, RIS, DOI lookups, and other citation formats; the de-facto Node ecosystem standard for citation parsing.
- bibcheck dependency: yes — for input parsing of `sources.json` and any non-CSL bibliography formats users provide.
- Why we don't replace it: it is the standard. Reimplementing CSL parsing in bibcheck would be pure duplication of well-tested work.

### Built-in `fetch` + small npm building blocks

`doi-regex`, `doi-utils`, `@jamesgopsill/crossref-client` and similar tiny modules. bibcheck's existence-check layer uses `fetch` directly and pulls in only minimal helpers. No heavyweight CrossRef wrapper library; the CrossRef and OpenAlex REST APIs are simple enough to call directly with appropriate polite-pool headers.

## Active tools we explicitly do not replace

Tools with **partial overlap** to bibcheck. Each covers some subset of bibcheck's scope but differs in input format, language, or feature shape such that bibcheck is not redundant — and where bibcheck's existence-check layer would otherwise compete with these, it does so deliberately as a thin convenience rather than as a feature.

### `citeclaw`

- Package: <https://www.npmjs.com/package/citeclaw>
- Status: Active, last release 2026-03.
- Language: Node.js.
- What it does: DOI / URL / PDF → metadata resolution, wrapping Citoid + CrossRef + Semantic Scholar + Zotero translators.
- Overlap: covers richer existence-check resolution than bibcheck's thin convenience layer. **No overlap on the differentiated bibcheck features** (canonical-edition whitelist, phrase-denylist lint surface, structured worklist, SARIF output).
- bibcheck stance: documented as the recommended deferral target when users need richer existence checking than bibcheck's commodity layer provides. Agent can invoke `citeclaw` directly for cases where bibcheck reports `not-found-in-databases` for a load-bearing entry.

### `FiCi`

- Repo: <https://github.com/sadjadeb/FiCi>
- Status: Active, last release 2026-04.
- Language: Python (PyPI).
- What it does: PDF input → reference extraction → OpenAlex → CrossRef → arXiv with `rapidfuzz` title scoring.
- Overlap: covers existence verification well, but PDF input only — does not consume CSL JSON or markdown.
- bibcheck stance: defer to FiCi for PDF-input projects. bibcheck targets the CSL JSON + markdown audience.

### `ValiRef`

- Repo: <https://github.com/Gianthard-cyh/ValiRef>
- Status: Active, last release 2026-04. ~48 stars.
- Language: Python (PyPI as `valiref`).
- What it does: multi-source verification (ArXiv, Semantic Scholar, OpenReview, OpenAlex, DuckDuckGo) plus LLM-based ReAct judging for counterfactual / irrelevant citations. Heavyweight; requires a DeepSeek API key.
- Overlap: more ambitious than bibcheck's scope (also catches "real source, claim not supported" — see [`../failure-modes.md`](../failure-modes.md) §8). PDF input only.
- bibcheck stance: defer to ValiRef when LLM-driven content-layer checking is wanted. bibcheck's worklist + the `reference-librarian` agent's manual layer fill the same need without paid LLM API dependency.

### `cite_verify_cli`

- Repo: <https://github.com/panchambanerjee/cite_verify_cli>
- Status: Active, last release 2026-02. ~3 stars.
- Language: Python.
- What it does: PDF / arXiv input → CrossRef + arXiv + Semantic Scholar + Unpaywall + 6-dimension quality score + BibTeX export.
- Overlap: closest in *spirit* to bibcheck's framing as a citation-quality CLI. PDF-input-shaped; STEM-shaped.
- bibcheck stance: complementary, not competing. Different input format and different audience.

### `lizTheDeveloper/citation-checker`

- Repo: <https://github.com/lizTheDeveloper/citation-checker>
- Status: Active 2026-02. ~4 stars.
- Language: Python (pre-commit hook).
- What it does: pure regex + local allowlist for flagging "suspicious" citations. Explicitly positioned as a git pre-commit hook for AI-hallucinated citations.
- Overlap: closest to bibcheck's *use-case framing* of any tool surveyed. But: trivial implementation — no API verification, no canonical-edition awareness, no markdown/prose-aware lint with file:line anchors and acknowledgement markers.
- bibcheck stance: bibcheck is a more substantive treatment of the same use case. The git-hook integration pattern is worth borrowing.

### `mufanq/verify-bib-skill`

- Repo: <https://github.com/mufanq/verify-bib-skill>
- Status: Active 2026-04. ~1 star.
- Language: Python CLI + Claude Code skill.
- What it does: BibTeX → Semantic Scholar match.
- Overlap: BibTeX-only; single-source (Semantic Scholar, which has poor humanities and pre-DOI coverage); modern-CS-paper-shaped.
- bibcheck stance: complementary; targets a different audience.

### `refcheck-mcp` (`benchoi93/refcheck`)

- Repo: <https://github.com/benchoi93/refcheck>
- Status: Active.
- Language: MCP server, not a CLI.
- What it does: citation verification via Model Context Protocol — exposed to LLM agents as a tool.
- Overlap: complementary distribution channel. bibcheck is a CLI; refcheck-mcp is an MCP server. Either can be wrapped to expose the other's surface.
- bibcheck stance: the MCP-server distribution pattern is interesting; if bibcheck ever wants an MCP front-end (in addition to or instead of being invoked via Bash), this is the reference implementation to look at.

### `CiteVerifier` (NKU-AOSP-Lab)

- Repo: <https://github.com/NKU-AOSP-Lab/CiteVerifier>
- Status: Active 2026-02. ~5 stars.
- Language: Python.
- What it does: DBLP-first citation verification — i.e., CS-conferences-and-journals-shaped.
- Overlap: useless for humanities; mentioned for completeness.
- bibcheck stance: orthogonal.

### Domain-specific legal-citation tools

`sgcite`, `hallucite`, `CiteShield`, `courtlistener_citations_mcp`, `BryanTegomoh/citation-audit`. Active 2025–2026, all Python, all targeting US legal citations. Mentioned for completeness — they show that the AI-citation-verification problem has been fragmenting into domain-specific tooling. bibcheck's positioning as the *humanities* tool fits the same fragmentation pattern.

## BibTeX style linters (different problem)

These tools lint BibTeX files for *formatting* and *missing-field* problems, not for whether the cited works exist. Different problem from bibcheck; mentioned to disambiguate.

- `bibcop` — <https://github.com/yegor256/bibcop> (Perl/LaTeX, ~15 stars, active 2026-04).
- `assorted-pre-commit-hooks/bibtex` — <https://github.com/assorted-pre-commit-hooks/bibtex> (pre-commit hook for BibTeX format).
- `dvolgyes/reflint`, `s-heppner/python-bibtex-linter`, `AliaumeL/bibadac`, `johndoe31415/biblint` — all BibTeX static linters; none hit the network to verify existence.

## Adjacent libraries and small building blocks

- `anystyle` — <https://github.com/inukshuk/anystyle> (Ruby, ~1231 stars). Reference-string parser. Out-of-process dependency option if bibcheck ever needs to parse free-text references rather than structured CSL.
- `rehype-citation` — npm, active 2026-02. Renders bibliographies in rehype pipelines; doesn't verify.
- `crossref-cli-tools` — <https://www.npmjs.com/package/crossref-cli-tools> (npm, active 2026-03). Lightweight Node CrossRef-only CLIs. Reference for API-call patterns rather than a dependency.

## Proprietary web tools (not open source)

These are referenced in [`../tooling-landscape.md`](../tooling-landscape.md) for completeness but are not open-source CLIs and therefore not options to depend on or replace:

- AiCitationChecker — <https://aicitationchecker.org/>
- SwanRef — <https://www.swanref.org/>
- GPTZero hallucination detector — <https://gptzero.me/hallucination-detector>
- scite.ai Reference Check — <https://scite.ai/blog/reference-check-an-easy-way-to-check-the-reliability-of-your-references-b2afcd64abc6>
- Trinka Citation Checker — <https://www.trinka.ai/features/citation-checker>

All target STEM citations and journal articles; weak on non-DOI primary sources; closed-source.

## What no surveyed tool does

These are the differentiated functions that justify bibcheck:

1. **Canonical-edition URL verification for pre-DOI primary sources.** No surveyed tool has a humanities-canonical-edition trusted-host whitelist (HathiTrust, Internet Archive, Liberty Fund OLL, SEP archives, PhilPapers, national-library catalogues) or verifies that pre-DOI bibliography entries point to a trusted edition rather than an arbitrary reprint URL. **The single strongest gap.**
2. **Generic project-supplied phrase-denylist lint over markdown/prose with file:line anchors.** No surveyed tool exposes a configurable regex denylist over prose with `<!-- bibcheck-allow: <key> -->` style acknowledgement markers, structured per-finding output, and CI-integration via SARIF. Useful for project-specific style-guide deprecations, retracted-source wording, or in-house terminology drift. **bibcheck does not ship a curated baseline of famous misattributions** (Voltaire-via-Hall, Burke "all that is necessary…", Tocqueville-on-America, Smith "invisible hand of the market" misuses, etc.) — that responsibility lives in the paired `reference-librarian` agent's domain knowledge ([`agent.md`](agent.md) §5), not in the tool. Quote Investigator (<https://quoteinvestigator.com/>) is the authoritative arbiter for cases the agent encounters that aren't in its prompt's example list.
3. **CSL-JSON-first input.** All the existence-verification tools surveyed take PDFs or BibTeX. CSL JSON — the modern, Pandoc-and-Zotero-canonical machine format — is not their native input.
4. **Pandoc-citeproc-style linkage check as a deterministic CI step.** `pandoc --citeproc` itself does this at *render time*; no surveyed tool exposes it as a deterministic pre-render CI gate with structured output.
5. **SARIF output for CI/PR-annotation integration.** No surveyed tool emits SARIF, which is how findings reach GitHub Actions PR annotations natively.

Item 1 is the strongest differentiator — genuine empty-space in the open-source landscape, aligned with the humanities / political-theory audience bibcheck targets. Item 2's framing changed late in the design (originally a curated misattribution baseline; now a generic lint surface with no baseline) — see [`agent.md`](agent.md) §5 for the rationale.

## Verdict

bibcheck is not redundant. The narrow-scope decision (see [`README.md`](README.md) §"Resolved decisions") makes the differentiation explicit: bibcheck does the humanities-aware verification layer no other tool does, depends on `citation-js` for parsing, and defers to `citeclaw` / `FiCi` / `ValiRef` for richer commodity work. The convenience-layer existence check is included so users have a working end-to-end tool, not as a competitive feature.

**Honest residual risk:** new tools enter this space frequently (the field has been active throughout 2025–2026). If a tool emerges that does the canonical-edition whitelist or the markdown/prose phrase-denylist lint as well as bibcheck would, the right move is to deprecate bibcheck's overlapping piece and depend on the new tool — not to compete on incumbency. This is the per-component licensing logic ([`../../licensing.md`](../../licensing.md)) applied to scope: own only what no one else owns; concede gracefully when the commons catches up.

## How to maintain this survey

This file is a snapshot. The Node and Python citation-tooling space is moving quickly; expect this survey to be partially stale within months.

Re-run the survey:

- Annually as a default cadence.
- Whenever a contributor reports a tool that overlaps with bibcheck's scope.
- Before any major version release of bibcheck (re-verify the differentiation still holds).

Each re-run should update this file with the date of the survey, any newly-found tools, and any tools that have meaningfully changed scope. Tools that have been abandoned (no commits in 18+ months) can be moved to a "Historical" subsection rather than deleted, since linking back to them remains useful.
