# `reference-librarian` agent + `bibcheck` tool — design memory

This folder is Mündig's design memory for a global Claude Code agent (`reference-librarian`) and a separate deterministic CLI tool (`bibcheck`) that together implement the citation-verification policy in [`../policy.md`](../policy.md).

The two artefacts are intended to be **reusable beyond Mündig** — any AI-assisted research project that handles citations could use them. Mündig is the first user; neither is Mündig-coupled.

> The detailed bibcheck design — subcommands, interface contract, implementation, and v0.1 build plan — now lives **in the bibcheck repository itself**, not here. See "Where bibcheck lives" below. This folder retains the *Mündig-side* design rationale: why these artefacts were built, what alternatives were considered, what design choices the project commits to and why.

## Why a split: LLM agent + deterministic tool

The same architecture academic copy-editing shops use, mapped onto LLM tooling.

| Layer | Best handled by | Why |
|---|---|---|
| Mechanical, reproducible operations (resolve 50 DOIs, check URL liveness, run a regex denylist) | Deterministic tool (`bibcheck`) | Reproducibility, runs in CI, can be run by anyone without an LLM, no hallucination risk |
| Judgment calls (which canonical edition? does this database disagreement matter? is this Wikipedia citation load-bearing or incidental?) | LLM agent (`reference-librarian`) | Pattern matching, contextual reasoning, ability to fetch and read primary sources |
| Synthesis (combine the tool's findings with judgment calls into a usable verification report) | LLM agent invoking the tool | Tool emits structured data; agent reasons about it |

**Putting both in the LLM** = non-deterministic, non-reproducible verification, no CI integration. Bad.

**Putting both in the tool** = no judgment about edge cases, no manual-layer triage, no ability to actually fetch and read a canonical edition for content verification. Also bad.

The split is load-bearing. The interface between the two — what the tool emits, what the agent does with that output — is the design's most important seam.

## Where bibcheck lives

`bibcheck` was scaffolded as a standalone repository on **2026-04-26** at `/Users/rmyers/repos/dot/bibcheck/` (sibling to this Mündig repo locally; intended to be published to a public GitHub URL once the first release is cut).

Inside that repo:

- `README.md` — public-facing tool description, generic to any docs project (no Mündig assumptions).
- `LICENSE` — MIT.
- `package.json`, `tsconfig.json`, `src/cli.ts`, `src/index.ts`, `src/schema/output.ts` — Node.js / TypeScript scaffold; the Zod-defined output schema is the contract any consumer reads.
- `doc/plan/` — full v0.1 implementation plan: 6 cross-cutting documents (architecture, phases, coding standards, testing strategy, release pipeline) and 19 task tickets covering all subcommands, output renderers, CI/release, and documentation. Designed for parallel agent work with an 80% coverage gate.
- `test/schema.test.ts` — smoke tests against the output schema.

When the bibcheck repo is published, this section's local-path references can be replaced with public URLs.

## File map for this folder

- [`agent.md`](agent.md) — design of the `reference-librarian` global Claude Code agent: tools whitelist, prompt-key-points, output convention, operational modes.
- [`integration.md`](integration.md) — how the agent and tool collaborate; the contract between them, three-leg orchestration (agent + bibcheck + commodity citation tools), workflow examples, failure modes.
- [`prior-art.md`](prior-art.md) — 2026 survey of existing open-source citation-verification tooling (FiCi, ValiRef, citeclaw, citation-js, refcheck-mcp, etc.); what each does, why bibcheck does not duplicate them, which we depend on or defer to. Includes a maintenance protocol so the survey can be re-run.

## Naming

- **Agent: `reference-librarian`.** Captures the persona — someone who checks rigorously, knows the bibliographic databases, declines to bullshit, defaults to "I haven't verified that yet" rather than fabricating a finding. Alternatives considered: `bibliographer`, `citation-auditor` — both narrower and less evocative.
- **Tool: `bibcheck`.** Short, descriptive, no obvious npm registry name collision as of January 2026. Alternatives considered: `cite-check`, `cite-verify`, `bibverify`.

## Reusability and packaging

The two artefacts have different distribution paths:

### `bibcheck` (the tool)

- **Standalone public repository**, separate from Mündig. Independent release cadence; benefits from a clean package-only repo.
- **MIT licence**, chosen at the moment the component took shape per Mündig's per-component licensing convention ([`../../licensing.md`](../../licensing.md)). Tooling that helps readers verify and contest claims has strong arguments for permissive distribution; MIT was the simplest fit.
- **Distribution via npm.** `npx bibcheck` runs the latest published version with no install; `npm i -g bibcheck` for permanent global use. Implementation is Node.js / TypeScript with strict TypeScript on the JSON output schema and `zod` for runtime validation. Node 20+ runtime; Node 24 required for the publish pipeline (npm Trusted Publishing via OIDC).
- **Documentation generic.** "Any docs project with a CSL JSON bibliography." Mündig is not the brand. Examples and tests should not assume Mündig conventions.

### `reference-librarian` (the agent)

- **Lives at `~/.claude/agents/reference-librarian.md`** — already shareable by copying.
- **Could be published as a Claude Code plugin** once the plugin format stabilises.
- **Prompt is generic** — does not reference Mündig. Loads the project's `bibcheck.toml` and `sources.json` as inputs; everything else is project-agnostic.

## Resolved decisions (Mündig-side rationale)

The decisions below are recorded as Mündig's design memory; the operational consequences live in the bibcheck repo.

- **Scope is narrow.** The 2026 prior-art survey ([`prior-art.md`](prior-art.md)) found the existence-checking core (CrossRef / OpenAlex / ISBN lookup) is well-covered by existing tools (`citation-js`, `citeclaw`, `FiCi`, `refcheck-mcp`, and others). bibcheck does **not** compete in that space. Its differentiated value is in four things no other surveyed tool does: canonical-edition URL verification for pre-DOI primary sources, deterministic Pandoc-citeproc-style linkage checking, structured human-triage worklist generation, and SARIF output for CI. A generic project-supplied phrase-denylist subcommand (`bibcheck phrases`) is also exposed as an opt-in lint surface, but **bibcheck ships no curated baseline**; the famous-misattribution responsibility lives in the `reference-librarian` agent's domain knowledge ([`agent.md`](agent.md) §5).
- **Implementation language: Node.js / TypeScript.** Decided over Python and Go. Node was chosen for distribution friction (npm/npx is lower-friction for non-developer users than virtualenv discipline), TypeScript for the schema-as-contract architecture (Zod schemas + `z.infer` types make the output contract enforceable at compile time), and toolchain familiarity (the user's existing CLAUDE.md notes on npm Trusted Publishing inherit known-good publish mechanics).
- **Two repos, not one.** `bibcheck` lives in its own repo; the `reference-librarian` agent definition is a single file at `~/.claude/agents/`. Their release cadences and packaging needs differ; they evolve independently.
- **Depend on `citation-js`** for CSL JSON / BibTeX / RIS parsing. The Node ecosystem standard for this; no reason to reimplement.

## Open questions (still Mündig-relevant)

The bibcheck repo's own plan handles its internal open questions. The questions that remain Mündig-side:

1. **CSL JSON `note:`-field convention for canonical-edition discipline.** Mündig wants to encode edition signals (e.g., `Ak. V`, `Glasgow WN`, `CW Mill X`) so bibcheck's `canonical` subcommand can verify URLs against the right canonical-edition host. The minimal v0.1 convention is to put the signal in CSL's free-form `note:` field. Mündig may want a more structured extension later — to be revisited when the citation inventory is consolidated into `docs/sources.json`.
2. **Quote-level fuzzy matching.** Whether Mündig adopts (or builds) a sister tool that fuzzy-matches quotes against open primary-source corpora. Considered, scoped, and deferred — see [`../quote-verification.md`](../quote-verification.md). Reconsider once bibcheck v1 has been in use for a few months and the worklist's actual shape is known.
3. **`reference-librarian` agent prompt drafting.** The agent design in [`agent.md`](agent.md) specifies the persona and tool whitelist; the actual system-prompt text has not been drafted. Probably the next concrete deliverable on the Mündig side.

## Cross-references

- [`../README.md`](../README.md) — parent source-verifier folder; the broader design memory.
- [`../policy.md`](../policy.md) — the four-layer verification policy these artefacts implement.
- [`../tooling-landscape.md`](../tooling-landscape.md) — the bibliographic-database and canonical-edition landscape the agent and tool both rely on.
- [`../failure-modes.md`](../failure-modes.md) — the citation-hallucination taxonomy the agent and tool target.
- [`../quote-verification.md`](../quote-verification.md) — deferred sister-tool design for Layer 2 quote verification against primary-source corpora.
- [`../citation-inventory.md`](../../citation-inventory.md) — the working inventory that will seed Mündig's `docs/sources.json` once vetted.
