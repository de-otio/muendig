# Mündig

> A De Otio research project: building blocks for healthier social media.

📖 **Read the docs:** **[de-otio.github.io/muendig](https://de-otio.github.io/muendig/)**

*Mündig* (German, "of age, autonomous, capable of speaking for oneself") is from Kant's *(Un)mündigkeit* in *Was ist Aufklärung?* (1784) — the human condition of being held in self-imposed or imposed tutelage, and the call to emerge from it. The project's working thesis: today's dominant social-media platforms keep their users in a kind of infrastructural tutelage — a *technofeudal vassalage* — and the way out is not yet another platform but the *building blocks* of one, in forms that resist concentration.

## What this project produces

Mündig is a research project. Its intended output is **technical components** — code libraries, reference implementations, design patterns — that can be used as foundations or inspiration for social-media tooling congruent with deliberative norms (Habermas's *communicative action*, the conditions for opinion-formation, plural counterpublics) and incongruent with the techno-feudal arrangement.

Mündig is *not* trying to build a complete social-media platform. The honest scope question — whether Mündig is a general-purpose toolkit or a focused *deliberative publication and discussion* toolkit — is open and discussed in [docs/techno-feudalism/habermas/deliberative-social-app/open-questions.md](docs/techno-feudalism/habermas/deliberative-social-app/open-questions.md).

## What kind of project this is

First and foremost, Mündig is a project done for the joy of it — a way to learn an interesting body of thought and to experiment with AI-assisted research as a method on a topic worth thinking about. It is **not** an academic, peer-reviewed research project, and it does not claim the authority of one: there is no domain expert in the loop, no peer review, no institutional vetting.

What it does aim at is an honest *effort* toward scientific standards — cited sources, confidence calibrated to the evidence, disclosed method, openness to correction. The aspiration is quality; the standing is that of an enthusiast learning in public. Read the docs as a careful learner's working notes, not as settled scholarship. [`docs/ai-assisted-research.md`](docs/ai-assisted-research.md) sets out what that means in practice and the failure modes it invites.

## Two evaluation tests

Every component proposed for Mündig is evaluated against:

1. **The techno-feudal test:** *does this widen or narrow the individual's path off the platforms?*
2. **The Habermasian test:** *does this favour communicative action (orientation toward mutual understanding) or strategic action (orientation toward influencing behaviour)?*

A component that fails either test is not a Mündig component.

### What the tests point toward: Public AI

The two tests are *filters* — they say what to avoid. The constructive name for
what they point toward is **Public AI**: digital infrastructure with *public
attributes* (openness, interoperability), *public functions* (serving knowledge
sharing, participation, and civic discourse rather than private gain), and
*public control* (governance for accountability, not capture). It is a useful
distinction from adjacent ideas — not merely *open-source* (openness at the code
level only), not *sovereign* (national competitiveness), not only *ethical*
(harm-mitigation inside an extractive structure), but *public*: governed in the
public interest, with reciprocity. Mündig's building blocks are intended as
candidate **Public AI components** for the public-knowledge ecosystem. The frame
is descriptive, not prescriptive — Mündig offers building blocks; it does not
tell anyone they *ought* to use them. See [`docs/research-landscape.md`](docs/research-landscape.md#public-ai--the-public-knowledge-coalition)
for the institutions and policy context now forming around this idea.

## Documentation

The research underpinning the project lives in [`docs/`](docs/). Start with [`docs/README.md`](docs/README.md) for an orientation map.

Key entry points:

- [docs/techno-feudalism/README.md](docs/techno-feudalism/README.md) — the orienting critique: why mainstream social media has refeudalized.
- [docs/techno-feudalism/enlightenment-lineage.md](docs/techno-feudalism/enlightenment-lineage.md) — the philosophical genealogy (Locke, Kant, Mill, Wollstonecraft, Smith, etc.).
- [docs/techno-feudalism/habermas/](docs/techno-feudalism/habermas/) — the bridge text. Habermas already used the word *refeudalization* in 1962 for what mass media did; the techno-feudal argument continues his.
- [docs/techno-feudalism/habermas/deliberative-social-app/](docs/techno-feudalism/habermas/deliberative-social-app/) — the design sketch this project is building toward.

## Status

Early-stage research; no code yet. The work currently consists of design notes in [`docs/`](docs/); the implementation phase begins once scope and approach are settled enough to make a first slice tractable.

The repository is public from the start, which carries some specific expectations:

- **Docs change.** Framings get revised, sections get rewritten, scope tilts. Git history is the project's record of thinking; current state is the best read at the time of reading, not the final position.
- **The research is AI-assisted** (see [`docs/ai-assisted-research.md`](docs/ai-assisted-research.md) for the disclosure and the failure modes that come with the method). Citation corrections, pointers to literature, and philosophical pushback are explicitly welcomed.
- **Progress will not be steady.** Research-phase work has long quiet stretches and bursts of activity; the absence of recent commits is not the absence of thinking.
- **The project is not optimised for newcomer hand-holding.** Issues and pull requests are welcome; see [`CONTRIBUTING.md`](CONTRIBUTING.md) for what kinds of engagement land best and what kinds won't.

## A note on method

The author is a technologist, not a philosopher; the research in [`docs/`](docs/) is AI-assisted. This is no substitute for reading the primary texts under proper guidance, but it is the honest situation, and open-sourcing the project is in part an invitation for better-read readers to correct errors and misreadings. See [`docs/ai-assisted-research.md`](docs/ai-assisted-research.md) for the longer disclosure — what AI is used for, the specific failure modes to watch for, and why this methodology is itself a live test case for the project's own framework.

## Licensing

The project will eventually be open source under a **per-component** licensing approach: each library or reference implementation gets its own licence, decided when the component takes shape rather than committed up front. See [`docs/licensing.md`](docs/licensing.md) for the framework — the two project tests applied to licence choice, default leanings by component type (AGPLv3 for reference implementations, Apache 2.0 for specs), and the cautionary case on permissive licensing in the deliberative-platform space.

**Current legal state.** No licence has yet been granted to any portion of this repository. Until per-component licences are applied, all rights are reserved by default; public visibility on GitHub does not imply public-domain status. Reading, quoting with attribution, and engaging via issues or pull requests are welcomed; redistribution, derivative works, or use as a basis for other projects require permission until specific licences are granted.

## About De Otio

[De Otio](http://de-otio.org) is the parent organisation. Its mission is to provide state-of-the-art technological tools to creative thinkers, activists, artists, and similar publishers — guided by the values of creative autonomy, individualism, free thought, freedom of expression, and the spreading of joy. Mündig follows from that mission directly.

De Otio also develops [**Trellis**](https://github.com/de-otio/trellis), an open social-network platform core (public-interest digital infrastructure for the social layer). Mündig and Trellis are complementary: Trellis is the generic social substrate; Mündig researches the deliberative *building blocks* — composer affordances, structured-reply templates, validity-claim tagging — that could sit beside or atop such a substrate. Both belong to the same emerging coalition of public-knowledge infrastructure (see [Public AI](#what-the-tests-point-toward-public-ai)).
