# Integration — how the agent and the tool collaborate

The interface between [`reference-librarian`](agent.md) (LLM-side judgment) and `bibcheck` (deterministic checks; see [`README.md`](README.md) for where the tool lives) is the design's most important seam. This file documents the contract, the typical workflows, and the failure modes of the collaboration.

## The contract

Two artefacts pass between the two layers:

### 1. Tool → agent: structured JSON

`bibcheck`'s default output for tool consumption is a versioned JSON schema. The schema lives in the bibcheck repository at `src/schema/output.ts` (Zod definitions; the auto-generated JSON-Schema documentation is in the bibcheck repo's `docs/output-schema.md` once published). The agent invokes `bibcheck check --format json` (or a more specific subcommand), parses the result, and reasons about it.

The contract is **stable, versioned, and documented**. The agent depends on the schema_version field; mismatches surface as a failure rather than as silent confusion.

### 2. Agent → tool: invocation parameters

The agent invokes the tool via `Bash`. The standard invocations:

- `bibcheck check --format json` — full audit, all layers.
- `bibcheck existence --citekey <key> --format json` — verify a single entry.
- `bibcheck phrases --format json` — phrase-denylist lint only (no-op when `[phrases].file` is unset).
- `bibcheck worklist --format json` — Layer 2 / Layer 3 worklist.
- `bibcheck doctor` — bootstrap diagnostic when something is misconfigured.

The agent does *not* pass arbitrary content through stdin or as command arguments — the tool reads from `sources.json` and the docs tree, both of which are filesystem inputs. This keeps the tool's input surface small and auditable.

## Typical workflows

### Workflow A: "Verify a doc"

User asks the agent: *"Verify the citations in `docs/techno-feudalism/enlightenment-lineage.md`."*

1. Agent reads the doc and the project's `sources.json`.
2. Agent invokes `bibcheck check --include 'docs/techno-feudalism/enlightenment-lineage.md' --format json`.
3. Agent parses the JSON output.
4. For each entry the tool flagged as `verified`, the agent reports it as Layer-1-verified, no further action.
5. For each entry the tool flagged as `metadata-mismatch` or `not-found`, the agent reasons about it:
   - Is this a database coverage gap (humanities, pre-DOI, non-English) where a `not-found` is inconclusive?
   - Or is it a real bibliographic error (wrong year, wrong publisher) the prose should fix?
   - The agent may invoke WebFetch directly against a canonical-edition URL to resolve.
6. For phrase-denylist matches (when the project has configured patterns), the agent reports them straight — these are unambiguous. The agent additionally surfaces any famous misattributions it notices from its own domain knowledge, regardless of whether the project has configured a denylist.
7. For worklist items, the agent reports them as a triage list with pre-filled verification URLs. Does *not* claim to have verified them.
8. Agent emits a structured verification report (see [`agent.md`](agent.md), §"Output convention").

The user — or a calling editing agent — then decides which findings to act on.

### Workflow B: "Verify the bibliography"

User asks: *"Audit `sources.json`."*

1. Agent reads the bibliography only.
2. Agent invokes `bibcheck existence --format json`.
3. Agent reports per-entry status. For `not-found` entries, agent does targeted WebFetch against canonical-edition URLs or national-library catalogues to manually verify.
4. Reports back with per-entry recommendations.

This is the cheaper, narrower mode — does not run the phrase-denylist lint or the worklist over the docs.

### Workflow C: "Check this one citation I'm not sure about"

User asks the agent in chat: *"Is this a real reference? Habermas, *On Digital Publics*, MIT Press, 2019."*

1. Agent does *not* immediately call `bibcheck` (the citation isn't in any bibliography file yet).
2. Agent fetches CrossRef directly via WebFetch / WebSearch.
3. Agent fetches OpenAlex directly.
4. Agent runs its own famous-misattribution check from domain knowledge (is this a Voltaire-via-Hall, Burke-"all-that-is-necessary", Tocqueville-on-America, etc.?). bibcheck ships no curated baseline; this check is the agent's, not the tool's.
5. Agent reports: "I cannot find a record matching this citation in CrossRef or OpenAlex. The closest matches are [...]. Recommend treating as unverified pending direct verification or correction by the user."

This mode does not require a bibliography file; the agent is operating standalone.

### Workflow D: "Bootstrap a project"

User adopts `reference-librarian` and `bibcheck` in a new project that has no `sources.json` yet.

1. Agent runs `bibcheck doctor`.
2. Tool reports: no `sources.json`, no `bibcheck.toml`.
3. Agent recommends seeding the bibliography from existing inline citations (analogous to Mündig's `citation-inventory.md` extraction).
4. After the inventory is reviewed and converted to CSL JSON, agent re-runs `bibcheck check`.

## Division of labour

A simple test for "which side does this belong to":

- *Is the operation deterministic and reproducible?* → Tool (`bibcheck`).
- *Does it require fetching content from a primary source (HathiTrust / IA / publisher PDF)?* → Agent.
- *Does it require judgment about which canonical edition to prefer?* → Agent.
- *Does it require regex pattern matching against a denylist?* → Tool (`bibcheck phrases`).
- *Does it require URL liveness or trusted-host whitelist verification for pre-DOI sources?* → Tool (`bibcheck canonical`).
- *Does it require commodity DOI / ISBN / title resolution against CrossRef or OpenAlex?* → Tool's thin layer (`bibcheck existence`) for simple cases; agent calls a heavier resolver (e.g., `citeclaw`) directly for richer needs.
- *Does it require parsing CSL JSON / BibTeX / RIS?* → Library (`citation-js`), used by both the tool and the agent.
- *Does it require reading prose and assessing whether a citation is being deployed appropriately?* → Agent.
- *Does it require assembling a structured report from multiple data sources?* → Either; the agent typically composes the final human-facing report from the tool's structured output plus its own findings.

## Three-leg orchestration: agent + bibcheck + commodity tools

The narrow-scope decision (see [`README.md`](README.md) §"Resolved decisions") means the agent orchestrates **three** layers, not two:

1. **`bibcheck`** for the differentiated humanities-aware checks (canonical-edition URL verification, linkage, worklist, opt-in project-supplied phrase-denylist lint) and the convenience-layer existence check.
2. **Commodity citation tools** for richer existence verification when bibcheck's thin layer is insufficient: `citeclaw` for DOI/URL/PDF resolution, `FiCi` or `ValiRef` for PDF-input verification, `pandoc --citeproc` for full CSL rendering and at-render-time linkage, `citation-js` as a parsing library used by both tool and agent. See [`prior-art.md`](prior-art.md) for the full landscape.
3. **The agent itself** for judgment calls, content-layer manual verification, and synthesis.

In a typical "verify a doc" workflow (see Workflow A below), the agent invokes `bibcheck check` first; only if bibcheck reports `not-found-in-databases` for a load-bearing entry does the agent escalate to a commodity resolver or to direct WebFetch against a canonical-edition source. This keeps the common case fast while preserving access to deeper verification for the cases that need it.

## Failure modes of the collaboration

### Schema drift

The tool and the agent depend on the JSON schema staying compatible. Mitigation:

- Schema is semver'd from day one.
- Tool emits `schema_version` in every output.
- Agent checks the version; reports a clear error rather than silently misinterpreting a new schema.
- Both repos cite a tested-compatible version range.

### Agent over-trusts the tool

If the agent reports the tool's findings without the layer-distinction discipline (§"Layered verification, never skip layers" in [`agent.md`](agent.md)), the user gets a false sense of completeness — "Layer 1 verified" presented as "verified."

Mitigation: the agent's prompt requires it to name *which layer* every claim is verified at, and to surface the worklist explicitly as remaining work.

### Tool under-reports because of API gaps

OpenAlex / CrossRef / OpenLibrary / WorldCat all have humanities coverage gaps. A `not-found` from all four does not rule out the work's existence.

Mitigation:

- Tool output distinguishes `not-found-in-all-databases` (a strong signal) from `unverifiable-via-databases-but-canonical-edition-URL-present` (no signal of fabrication, just outside the API stack's coverage).
- Agent's prompt includes the explicit honesty rule: "for pre-DOI primary sources and humanities monographs, every automated tool has known coverage gaps; a 'no record' is not dispositive."

### Agent fabricates verification when tool returns inconclusive

The most dangerous failure: the agent has access to WebFetch and WebSearch, and could in principle hallucinate a URL it didn't actually fetch.

Mitigation:

- Agent's prompt includes: "Report evidence, not just verdicts. Every claim ... should be accompanied by which database / canonical-edition URL it consulted, what that source returned, what inference the agent drew."
- Output schema requires evidence fields per claim.
- Audit: a reviewing human can cross-check the agent's report by fetching the same URLs.

### Tool drift from emerging best practice

API endpoints change; new databases emerge; coverage shifts. The tool's `existence` checker can become stale.

Mitigation:

- Tool versioned independently; users pin a tested version range.
- Tool's database backends are pluggable so new ones can be added without breaking the schema.
- Phrase-denylist patterns: project-supplied via `[phrases].file`, not bundled with bibcheck. The agent's famous-misattribution domain knowledge is updated by editing the agent's prompt in the [`claude-toolkit-richard`](../../../../claude-toolkit-richard/agents/reference-librarian.md) repo when new canonical examples warrant inclusion.

## When *not* to use the tool

Some verification work is faster and cleaner without `bibcheck`:

- A single citation check during writing — invoke the agent, let it use WebFetch directly. Don't bother round-tripping through the tool.
- A project too small to warrant a `sources.json` — direct agent verification on inline citations is appropriate.
- Quick-and-dirty drafts where citations are placeholder. The tool only helps once a bibliography file exists and is intended to be canonical.

The tool's role is amortised across many citations and many runs; for one-shot work the agent alone is enough.

## When *not* to use the agent

The tool can be invoked directly without an agent:

- CI / automation contexts where deterministic, scriptable verification is the entire requirement.
- Editor integrations (VS Code, etc.) — the tool's SARIF output renders natively without an LLM in the loop.
- Bulk verification of a large bibliography by a human copy editor who wants a worklist, not a conversation.

The agent's role is judgment; for purely mechanical needs, skip it.

## Forward compatibility

A few design choices preserve the option to evolve the collaboration:

- **Stable JSON schema** with semver — agent and tool can move at different speeds.
- **Tool plugins** for new database backends — adding e.g. JSTOR or PhilPapers as a backend doesn't break the agent.
- **Agent prompt is tool-version-agnostic** — describes the abstract workflow rather than the exact CLI surface; refers to "the bibliography-verification tool" generically.
- **Both artefacts are open-source** — third parties can contribute backends, denylist entries, or replace either side independently.
