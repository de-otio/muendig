# Licensing — A Per-Component Approach

The project does not have a single licence. Each component will be licensed individually, with the decision made when the component itself takes shape — not before.

## Why per-component

The project produces "technical components — code libraries, reference implementations, design patterns" (see the [project README](https://github.com/de-otio/muendig/blob/main/README.md)). Different component types call for different licences: reference implementations that compete with platform features should resist absorption; specs and protocols should propagate freely; small generic primitives may benefit from maximum adoption surface. A single project-wide licence collapses these distinctions.

## The decision lens

Two tests, the project's own:

1. **Techno-feudal test:** does this licence widen or narrow the individual's path off the platforms?
2. **Habermasian test:** does this licence favour communicative (reciprocal) or strategic (extractive) norms?

A third practical question per component: *who is the natural adopter, and is this licence congenial to them?*

## Default leanings (starting points, not commitments)

- **Reference / runtime / server implementations** → start at **AGPLv3**. The network-service share-alike clause, plus the deterrent effect of widely-reported AGPL bans at major platforms (verify current status before citing externally), gives the strongest anti-feudal protection. The fediverse precedent (Mastodon, Lemmy, PeerTube, Pixelfed) clusters here.
- **Pure specs and protocols** → start at **Apache 2.0**. The patent grant is itself anti-lord; spec-shaped artefacts benefit from broad adoption; restrictive licensing on specs tends to fragment ecosystems.
- **Small generic libraries / primitives** → case-by-case. If the worry is "platforms absorb without contributing," lean AGPL/LGPL. If the worry is "this needs to propagate everywhere or it's pointless," lean Apache 2.0.

Each per-component decision should run the two tests and the natural-adopter question fresh; defaults can flip on artefact specifics.

## Cautionary case

The conspicuous permissive choice in the deliberative-platform space is **Bluesky / AT Protocol (MIT)**. There is a live critique — circulated in fediverse-vs-Bluesky discourse from roughly 2023 onward — that the permissive licence enabled platform-lord recreation under a different banner. Whether the critique is correct is contested; treat it as a position-among-positions rather than settled. But the failure mode it names is exactly what an anti-feudal project should be alert to. Verify current state of this discourse before citing in external-facing material.

## On the pluralism objection

A real objection to defaulting to AGPL: forcing reciprocity could be read as imposing a single politics on downstream counterpublics. The counter is that reciprocity is *procedural* (it forces a sharing norm, not a substantive position on what is shared), and procedural reciprocity is congruent with the project's deliberative-pluralist stance. The objection is not fatal but it is real, and per-component decisions should weigh it where the component would in practice constrain a downstream community's licence choices.

## Procedural rule until the first component lands

No licence-implying files anywhere in this repository (no `LICENSE`, no `package.json` `license` field, no licence headers in source) until the first component exists and its licence has been decided per this framework. See the [agent instructions](https://github.com/de-otio/muendig/blob/main/CLAUDE.md) for the same rule from the AI-agent angle.
