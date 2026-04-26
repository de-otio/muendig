# Mündig — instructions for AI agents

## What this project is

Mündig is a research project producing technical components (code libraries, reference implementations) for social-media tooling that is congruent with deliberative norms and incongruent with the techno-feudal arrangement of mainstream platforms. See [`README.md`](README.md) for the project overview and [`docs/`](docs/) for the research underpinning it.

The project is named after Kant's *Mündigkeit* — the condition of being autonomous, of age, capable of speaking for oneself. The thesis: mainstream platforms keep users in a kind of infrastructural tutelage; Mündig builds the components that make the alternative tractable.

## When working in this repo

### Read the design notes before writing design code

The notes in [`docs/techno-feudalism/`](docs/techno-feudalism/) are not background reading — they are the project specification. Specifically:

- For *what is being built*: [`docs/techno-feudalism/habermas/deliberative-social-app/`](docs/techno-feudalism/habermas/deliberative-social-app/), particularly the [`moves/`](docs/techno-feudalism/habermas/deliberative-social-app/moves/) folder and [`de-otio-fit.md`](docs/techno-feudalism/habermas/deliberative-social-app/de-otio-fit.md) for buildable slices.
- For *why* it is being built that way: [`docs/techno-feudalism/habermas/`](docs/techno-feudalism/habermas/) (the framework) and [`docs/techno-feudalism/enlightenment-lineage.md`](docs/techno-feudalism/enlightenment-lineage.md) (the deeper genealogy).

### Apply the two tests

Every proposed component should be evaluated against:

1. **The techno-feudal test:** *does this widen or narrow the individual's path off the platforms?*
2. **The Habermasian test:** *does this favour communicative action (orientation toward mutual understanding) or strategic action (orientation toward influencing behaviour)?*

When proposing a design or a component, state how it fares against these. A proposal that fails either is not a Mündig proposal.

### Honest about open scope

The scope is genuinely open. Mündig might end up being a general-purpose social-media toolkit or a focused deliberative-publication-and-discussion toolkit — see [`docs/techno-feudalism/habermas/deliberative-social-app/open-questions.md`](docs/techno-feudalism/habermas/deliberative-social-app/open-questions.md). Don't pretend the scope is settled when it isn't. When working on something whose scope-fit is unclear, raise the question rather than proceeding with assumed scope.

### Push design learning back to the docs

When implementation work surfaces a design insight or invalidates a design assumption, update the relevant `docs/` file rather than letting the learning stay implicit in code. The docs folder is the project's persistent design memory.

### License is per-component, deferred until components exist

The project will be open-sourced under a per-component licensing approach: each library or reference implementation gets its own licence, decided when the component takes shape. See [`docs/licensing.md`](docs/licensing.md) for the framework (two-tests lens, default leanings by component type, the pluralism objection, the Bluesky cautionary case). Until the first component exists, do not add any licence-implying file anywhere — no `LICENSE`, no `package.json` `license` field, no licence headers in source.

## Conventions

- This is a docs-first repo at present. Code conventions will be added as the first slice of code lands.
- Markdown documentation uses GitHub-flavoured markdown.
- File and folder names: lowercase with hyphens (`deliberative-social-app/`, `enlightenment-lineage.md`).
