# Working policy

Proposed four-layer policy for citation verification, with the architecture sketch and open questions. Adopted in summary form in [`../ai-assisted-research.md`](../ai-assisted-research.md); detailed here for the persistent design memory.

## The four layers

### Layer 1 — Existence

Every cited work has an entry in the canonical bibliography (`docs/sources.json`, with `docs/sources.md` as a generated human-readable view). Each entry is verified against an open bibliographic database — CrossRef for DOI-bearing works, OpenLibrary or WorldCat for books with ISBNs, OpenAlex as a broad fallback particularly for non-English humanities — or against a canonical-edition URL where the source is pre-DOI. See [`tooling-landscape.md`](tooling-landscape.md) for the database list and coverage caveats.

This layer is automatable. The `verify-citations` skill (not yet built — see "Architecture" below) hits the relevant API per entry and reports any work that cannot be resolved or whose metadata mismatches the bibliography entry.

### Layer 2 — Content

No automation reliably checks whether a cited source actually says what the prose claims. Every load-bearing inline citation — direct quotations, paraphrases attached to a page reference, "Habermas argues X"-style attributions — has to be spot-checked against the source by a human reader.

The `verify-citations` skill emits a worklist of these for human triage; it does not claim to resolve them. The worklist pre-fills a verification URL where possible (HathiTrust / IA / Google Books search) so the human-side check is cheap.

The default for direct quotations is *paraphrase* unless the user has the source open and pastes the verified passage with edition. This is the rule already enforced in the citation-inventory edits — see e.g. the deferred-quotation move on Mill's harm principle in [`../techno-feudalism/enlightenment-lineage.md`](../techno-feudalism/enlightenment-lineage.md).

### Layer 3 — Context

Citations should be weighted appropriately. A peer-reviewed journal article in a recognised venue does not have the same epistemic authority as a conference paper, a Substack post, an encyclopaedia entry, or an LLM-generated summary. Where a citation is being deployed in a load-bearing role, the doc should make the source's status legible — peer-reviewed journal, scholarly edition, encyclopaedia entry, polemic — rather than presenting all citations as functionally equivalent.

This is partially automatable (source-type metadata can be extracted for some sources), but in practice this layer is enforced as a writing-rule rather than a lint.

### Layer 4 — Phrase-denylist + agent attentiveness to famous misattributions

Two complementary mechanisms catch the highest-reputational-risk-per-occurrence failure mode: a quote correctly transcribed but attributed to a person who never wrote it.

- **Project-supplied phrase-denylist** (when configured). bibcheck exposes a generic `bibcheck phrases` subcommand that lints prose against patterns the project supplies via `[phrases].file` in `bibcheck.toml`. Matches require an explicit `<!-- bibcheck-allow: <key> -->` acknowledgement inline. **bibcheck does not ship a curated baseline** — any baseline shipped with a generic tool would be both incomplete (a tiny slice of the misattribution universe) and reputationally load-bearing (a single bad pattern would taint the rest). For Mündig, a small project-level denylist may be added once the documented failures justify it; the agent's domain knowledge covers the case until then.
- **Agent attentiveness** (always). The `reference-librarian` agent ([`reference-librarian/agent.md`](reference-librarian/agent.md) §5) carries the responsibility for noticing famous misattributions during verification work — its system prompt enumerates ~12 canonical examples (Voltaire-via-Hall, Burke "all that is necessary…", Tocqueville-on-America, the Smith "invisible hand of the market" misuses, the Einstein/Twain/Churchill quote spectrum). Quote Investigator (https://quoteinvestigator.com/) is the authoritative arbiter for cases beyond that list. See [`failure-modes.md`](failure-modes.md) §7 for the full failure-mode taxonomy.

Existence-checking does not address this layer at all because the cited work is real and only the attribution is false.

## Architecture sketch

### Files

- **`docs/sources.json`** — canonical bibliography in CSL JSON. Generated from / synced with Zotero (Better CSL JSON exporter; "Keep updated" auto-rewrites). Each entry includes:
  - Required CSL fields (id, type, author, title, issued, container-title, publisher, etc.)
  - `note:` field recording the canonical edition in use (e.g., `Ak. V`, `Glasgow WN`, `CW Mill X`, `Burger 1989 MIT translation`)
  - `url:` field pointing to a verifying URL — the DOI URL where one exists, otherwise a canonical-edition URL (HathiTrust / IA / OLL / SEP archive / PhilPapers / national library)
- **`docs/sources.md`** — human-readable view, generated from `sources.json`. The file contributors and reviewers actually read.
- **`docs/citation-inventory.md`** — current working artefact ([`../citation-inventory.md`](../citation-inventory.md)), seeded from a one-time extraction over `docs/`. Each entry includes a confidence rating; once vetted entries are added to `sources.json`, this file can be retired.

### The `verify-citations` skill (not yet built)

Three passes:

- **Pass A — existence.** For every entry in `sources.json`: DOI → resolve via doi.org / CrossRef; ISBN → OpenLibrary or WorldCat; otherwise → OpenAlex; otherwise → the entry's `url:` field returns 200 and matches a publisher / canonical-archive domain whitelist.
- **Pass B — linkage.** Every `@citekey` referenced in `docs/**/*.md` has a corresponding entry in `sources.json`. This is what `pandoc --citeproc` already does — wrap and surface its failures.
- **Pass C — phrase-denylist lint** (opt-in). When `[phrases].file` is configured in `bibcheck.toml`, scan prose for entries on the project-supplied denylist. Each match without an explicit `<!-- bibcheck-allow: <key> -->` acknowledgement is a failure. No-op when no denylist is configured. (bibcheck ships no baseline; see Layer 4 above.)

The skill emits, in addition:
- A **content-verification worklist** of every direct quotation and every page-cited paraphrase, with a pre-filled verification URL.
- A **context worklist** flagging citations to source-types that warrant explicit weighting (encyclopaedia entries, preprints, blog posts) where the prose has not made the source-type legible.

### Build pipeline

`pandoc --citeproc` over the docs folder as a CI step. Unresolved cite keys = build failure. This is the gate that prevents new citations being introduced without an entry in `sources.json`.

### Protocol for AI-assistant agents

Baked into [`../../CLAUDE.md`](../../CLAUDE.md) (not yet added):

- AI agents may not introduce a citation that does not already have an entry in `sources.json`.
- New citations require human authorisation accompanied by a verifying URL.
- Direct quotations default to paraphrase markers unless the user pastes the verified passage with edition.
- The `philosophy-review` skill ([`../../.claude/skills/philosophy-review/`](../../.claude/skills/philosophy-review/)) and a future `verify-citations` skill are the audit counterparts.

## Where things stand

Done:

- Philosophy-review pass over the framework docs surfaced uncertain citations with `[verify]` markers.
- Citation inventory at [`../citation-inventory.md`](../citation-inventory.md): 43 cited works, with confidence ratings.
- Citation-verification policy section in [`../ai-assisted-research.md`](../ai-assisted-research.md).
- This folder of design notes.

Pending:

- Decide bibliography format (CSL JSON is the strong default; alternatives are CSL YAML or ad-hoc Markdown — see open questions).
- Decide citekey conventions (`@author-yearKeyword` is conventional; many alternatives).
- Build `docs/sources.json` from the inventory: vet each MEDIUM and MARKED entry against an authoritative bibliography or canonical-edition URL; transfer the HIGH entries with API spot-checks.
- Build the `verify-citations` skill (existence + linkage + phrase-denylist + worklist generation). The skill is now substantially superseded by `bibcheck` itself — see [`reference-librarian/`](reference-librarian/) for the agent + tool pairing.
- Add the protocol to `CLAUDE.md`.
- Decide whether the skill lives in this project (`.claude/skills/verify-citations/`) or globally (`~/.claude/skills/verify-citations/`); generic enough to be global, but bibliography file is project-specific.

## Open questions

1. **Bibliography file format.** CSL JSON is the strong default — Pandoc and Zotero speak it natively, build-time validation comes free with `pandoc --citeproc`. Alternatives: CSL YAML (more readable, same content); ad-hoc Markdown (loses the build gate). Default to CSL JSON unless there is a reason to deviate.
2. **Citekey convention.** Better BibTeX defaults to `@author-yearTitleKeyword` style; some projects prefer `@author-yearShortTitle`. Stable citekeys matter because they appear inline in prose.
3. **Edition discipline enforcement.** The cite-by-Akademie-page / Glasgow-paragraph rule cannot be enforced automatically (the skill cannot tell whether `IV.ii.9` is right for a given quote). Best handled as a writing convention noted in `CLAUDE.md` and surfaced in the philosophy-review skill.
4. **Mündig-side phrase denylist (if any).** bibcheck does not ship a curated baseline; the misattribution-catching role belongs to the `reference-librarian` agent. Whether Mündig itself adds a small project-supplied phrase denylist via `[phrases].file` in `bibcheck.toml` — for project-specific style-guide deprecations or retracted-source wording, not as a general-purpose famous-misattributions list — is a separate decision. Default: no project denylist; add patterns only on encounter with a specific failure that the agent missed and that's worth automating against.
5. **Skill scope vs. global tool.** The verification skill is generic — most of it is not project-specific. Living globally (in `~/.claude/skills/`) lets it benefit other projects; living in-project lets it evolve alongside Mündig's specific bibliography schema. The simplest first move is in-project; promote to global once the schema stabilises.
6. **Handling non-English humanities sources.** OpenAlex has the best coverage but humanities publishers (Brill, CAIRN, OpenEdition) are still under-indexed. For sources that fail all three database checks, the canonical-edition URL is the verifier — but that places more weight on the URL host's authority. National library catalogues (DNB, BnF) are the right backstop here but need API integration.
7. **What to do about Wikipedia, Substack, blog citations.** None of these belong in a published critical-theory project as load-bearing citations. The policy should make this explicit, perhaps via a `source-type:` field in `sources.json` and a soft warning when a load-bearing prose claim is anchored to a low-authority source-type.
8. **Translator and edition fields.** CSL JSON has fields for these but Zotero's translator-detection is uneven. For the named thinkers (Habermas, Negt/Kluge, etc.), translator metadata matters for the project — needs to be filled in by hand from the canonical edition. This is one place where the inventory's `[translators to verify]` markers will need human resolution.
9. **Layer 2 automation against primary-source corpora.** Whether to build (or adopt) a sister tool that fuzzy-matches quotations against open full-text repositories (Project Gutenberg, Internet Archive, Wikisource) to partially automate content verification. Considered, scoped, and deferred — see [`quote-verification.md`](quote-verification.md). Reconsider once bibcheck v1 is in use and the worklist's actual shape is known.

## Honest about what this does not solve

The four-layer policy reduces but does not eliminate citation risk. The residual risk lives in:

- **Database disagreements** — when CrossRef, OpenAlex, and OpenLibrary return inconsistent metadata, the human has to adjudicate. The skill flags these but cannot resolve them.
- **Layer 2 fatigue** — content verification scales linearly with the size of the worklist. A long-running project will accumulate a large worklist; whether human reviewers actually work through it determines whether the layer is real or notional. A sister-tool option for partially automating Layer 2 against open primary-source corpora (Project Gutenberg, Internet Archive, Wikisource) has been considered and deferred — see [`quote-verification.md`](quote-verification.md) for the analysis and the reconsider-when criteria.
- **Edition slippage** — cite-by-internal-numbering (Ak., Glasgow, CW) is robust against reprint changes, but only if every contributor follows it. A future contributor citing a Penguin paperback page number breaks the discipline silently.
- **Misattribution awareness drift** — famous misattributions accumulate over time. Since bibcheck ships no curated baseline, this responsibility lives in the `reference-librarian` agent's prompt and the agent's domain knowledge. Update the agent's example list periodically; cross-check Quote Investigator for new entries to add.

These are real costs and the policy is honest about them. Source verification is not a one-time fix but a continuing editorial discipline; the architecture's job is to make that discipline tractable rather than impossible.
