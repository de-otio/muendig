# Source verification — notes and working policy

This folder collects what the project has learned about verifying citations in AI-assisted research. Source verification turned out to be a substantial sub-problem in its own right; this folder is the persistent design memory for it.

## Why this matters for Mündig

The Mündig docs are research notes whose prose has been substantially AI-drafted ([`../ai-assisted-research.md`](../ai-assisted-research.md)). One of the documented failure modes (no. 3, "Confabulated citations and quotations") is the highest-reputational-risk failure for an open-source critical-theory project: a fabricated source, a real source quoted backwards, or a famous misattribution waved through would discredit the work as a whole.

The project's two evaluation tests apply to citation methodology itself:

- **Techno-feudal test.** A research project that cannot make its sources verifiable to readers narrows rather than widens the path off the platforms — readers are forced to trust the author the same way platform users trust the lord. Verifiable, openly-cited primary sources are anti-feudal infrastructure in the *Encyclopédie* sense.
- **Habermasian test.** Publishing AI-drafted prose with un-checked citations, in the same declarative voice as well-grounded prose, is *strategic* communication — it asks the reader to defer to a felt authority that the production process did not earn. Citations that the reader can independently verify are *communicative* — oriented toward mutual understanding, contestable on the merits.

Source verification is therefore not an editorial tax; it is structurally the same kind of move the project advocates for elsewhere.

## Current status (2026-04-26)

- A philosophy-review pass over the framework docs has surfaced and tagged uncertain citations with `[verify]` markers throughout `docs/techno-feudalism/`. See [`../techno-feudalism/`](../techno-feudalism/).
- A citation inventory of every cited work currently in `docs/` has been extracted to [`../citation-inventory.md`](../citation-inventory.md). 43 works, with confidence ratings.
- A working-policy section has been added to [`../ai-assisted-research.md`](../ai-assisted-research.md) describing the four-layer verification approach.
- This folder documents the underlying reasoning and the tooling landscape behind those artefacts.

What is *not* yet built:
- A canonical bibliography file (`docs/sources.json` in CSL JSON, with `docs/sources.md` as a generated human-readable view).
- A `verify-citations` skill that runs API checks + phrase-denylist lint + worklist generation. (Largely superseded by the `bibcheck` CLI + `reference-librarian` agent pairing — see [`reference-librarian/`](reference-librarian/).)
- A Pandoc-citeproc build gate that fails on unresolved citation keys.
- Verification of the entries in `citation-inventory.md` against authoritative bibliographies.

## File map

- [`failure-modes.md`](failure-modes.md) — taxonomy of citation-hallucination failure modes, ranked by reputational risk and by what is automatable vs. manual.
- [`tooling-landscape.md`](tooling-landscape.md) — what's available for verification: bibliographic databases, canonical-edition full-text repositories, philosophy-specific scholarly editions, citation-management toolchains, LLM-citation-verification tools. With coverage caveats per tool.
- [`policy.md`](policy.md) — the four-layer working policy (existence / content / context / phrase-denylist + agent-attentiveness), the proposed architecture, and open questions.
- [`reference-librarian/`](reference-librarian/) — design of the global Claude Code agent and the `bibcheck` CLI tool that together implement the policy.
- [`quote-verification.md`](quote-verification.md) — deferred-but-considered design for a sister tool that would partially automate Layer 2 by fuzzy-matching quotes against Project Gutenberg / Internet Archive / Wikisource. Reconsider after bibcheck v1 is in use.
- [`../citation-inventory.md`](../citation-inventory.md) — the working inventory of currently-cited works (lives in the parent `docs/` folder so it can also be referenced from [`../ai-assisted-research.md`](../ai-assisted-research.md)).

## How this folder relates to other parts of the project

- [`../ai-assisted-research.md`](../ai-assisted-research.md) — the project-level disclosure about AI-assisted method. The "Citation verification" section there is the public-facing summary; this folder is the working detail.
- [`../../.claude/skills/philosophy-review/SKILL.md`](../../.claude/skills/philosophy-review/SKILL.md) — the existing audit skill. Lens 1 (Citations) is what flagged the need for this work in the first place.
- A future `verify-citations` skill (not yet built) will be the executable counterpart to the policy described here.

## Honest about open scope

Several decisions are not yet made: bibliography file format (CSL JSON vs. YAML vs. ad-hoc Markdown), citekey conventions, the protocol that governs how AI agents are allowed to introduce new citations, and whether the verification skill should live in this project or as a global Claude Code skill. The notes below capture the current thinking; treat them as the state of the art, not the settled answer.
