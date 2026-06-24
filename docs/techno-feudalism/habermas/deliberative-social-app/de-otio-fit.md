# The De Otio Fit

> See [folder README](README.md) for orientation. The practical question is not "build the whole sketch" but "which slice is buildable, useful to the audience, and clearly anti-feudal in [the sense already developed](../../README.md)?"

Candidates:

- **Publication tooling congruent with the framework.** The publisher controls identity, hosting, audience relationship, and economics; the platform layer is thin or federated. (Aligns with De Otio's broader publication-tooling agenda for creative publishers facing the EU regulatory landscape.)
- **A federated discussion substrate** that publishers can attach to their work — comments, threads, deliberation — without surrendering the audience to a platform.
- **The communicative-action layer as a tooling kit** ([composer affordances, structured-reply templates, validity-claim tagging](moves/communicative-action-affordances.md)) that any publisher or community can adopt without rebuilding the platform.

A note on the substrate: none of these slices needs to be built on bare metal.
De Otio's [Trellis](https://github.com/de-otio/trellis) is a generic, federated
social-network core; the deliberative slices above are most naturally framed as
**building blocks that sit beside or atop** such a substrate rather than a new
platform from scratch.

### If a slice uses AI

The communicative-action tooling kit is the slice most likely to reach for AI
(e.g. summarising a thread, surfacing bridging viewpoints, suggesting a
validity-claim tag). Where it does, a design constraint follows from both tests
and from the Public AI frame ([README](../../../../README.md#what-the-tests-point-toward-public-ai)):

- **Small, domain-specific models over opaque general-purpose ones** — scoped to
  the task, inspectable, cheaper, and far lower in environmental cost.
- **Retrieval grounded in provenance-rich sources**, so outputs are traceable
  rather than confabulated.
- **No engagement-optimising objective** — assistance oriented toward mutual
  understanding (communicative action), never toward maximising interaction.
- **Commons-governed, not captured** — a model or tool that re-creates a
  dependency on a single opaque provider fails the techno-feudal test.

An AI feature that cannot meet these is a strategic-action tool wearing a
deliberative costume, and is not a Mündig component.

The full integrated platform is probably not the right first build for De Otio. The framework is more useful as an evaluator of any specific tool the org does build:

- *Does this widen or narrow the path off the platforms?* (the [techno-feudal test](../../README.md))
- *Does this favour communicative or strategic action?* (the [Habermasian test](../communicative-action.md))
