# A Note on Method — AI-Assisted Research

The Mündig research notes in [`techno-feudalism/`](techno-feudalism/) draw on philosophers — Kant, Locke, Habermas, Mill, Smith, Wollstonecraft, and others — many of whose primary works the author has not read in full. The author is a technologist, not a philosopher. The notes have been developed with substantial AI assistance: an LLM helps surface, integrate, and articulate ideas from thinkers whose work would otherwise take years of guided reading to engage with at this level.

This document is a disclosure about that method.

## Why be transparent about it

A research project that critiques opaque, lord-mediated knowledge infrastructures cannot in good faith hide that its own knowledge production runs through an opaque, AI-mediated layer. Publishing the methodology — and naming the failure modes — is the structurally honest move and is congruent with the deliberative norms the project is building toward.

## What the AI is used for

- Surfacing the relevant arguments of thinkers across long bibliographies, condensed enough to engage with within a research session.
- Drafting prose that the author then reads, edits, accepts, rejects, or rewrites.
- Cross-checking connections between thinkers and traditions; suggesting where a critique cuts, where it doesn't, what the standard objections are.
- Naming the project's own tensions — the AI is often a useful adversarial reader of the author's claims.

## What the AI is *not* a substitute for

- Reading the primary texts. The notes here represent received and condensed readings, not the author's first-hand engagement with the source material.
- A professor of philosophy. There is no domain expert in the loop verifying interpretations.
- Settled scholarship. Where the docs make confident-sounding claims about a thinker, those claims should be treated as starting points for the reader, not endpoints.

## Specific failure modes to watch for

These are the risks that bite this kind of work in particular. Readers who spot any of these in the docs should flag them.

1. **Smoothed-over disagreements.** LLMs tend to synthesise opposing positions into a bland middle. Real disagreements between, say, Locke and Rousseau or Habermas and Fraser may be papered over in summary form.
2. **The textbook reading by default.** What an LLM gives you is the most-cited received reading of a thinker. The contested, live, scholarly reading — the one a professor would push you toward — is harder to surface and is often more interesting.
3. **Confabulated citations and quotations.** LLMs invent quotes, dates, and titles. The "I disapprove of what you say…" line in [`techno-feudalism/enlightenment-lineage.md`](techno-feudalism/enlightenment-lineage.md) is correctly flagged there as a paraphrase rather than Voltaire's words; less famous misattributions are easier to miss. See "Citation verification" below for the working policy that addresses this failure mode specifically.
4. **Invisible ideological priors.** LLM training imports the framings and assumptions of its training corpus. A particular reading of a passage may slip in without being marked as a choice.
5. **Plausibility over accuracy.** AI-generated prose reads coherently even when it is wrong. The reader cannot use felt-authority as a guide to correctness.

## Citation verification — the working policy

Failure mode 3 (above) is the highest reputational risk for an open-source critical-theory project: a fabricated citation, a real source quoted backwards, or a famous misattribution waved through. The project's working policy — under construction — has four layers, adapted from reference-librarian and academic-publishing practice:

1. **Existence (automatable).** Every cited work has an entry in a canonical bibliography file. Each entry is verified against open bibliographic databases — primarily [OpenAlex](https://openalex.org/) (broad coverage including non-English and non-DOI works), [CrossRef](https://www.crossref.org/) (journal articles with DOIs), [OpenLibrary](https://openlibrary.org/) and [WorldCat](https://www.worldcat.org/) (books and editions) — and against a canonical-edition URL where the source is pre-DOI (HathiTrust, Internet Archive, the [Stanford Encyclopedia of Philosophy](https://plato.stanford.edu/) archived snapshots, [PhilPapers](https://philpapers.org/), Liberty Fund's [OLL](https://oll.libertyfund.org/) for primary texts in the Enlightenment lineage). For the named thinkers in this project — Kant, Locke, Smith, Mill, Habermas, Spinoza — citations target the canonical scholarly editions (Akademie-Ausgabe for Kant; the Glasgow Edition for Smith; the Toronto *Collected Works* for Mill; the Clarendon Edition for Locke) and use those editions' internal numbering rather than reprint pagination.
2. **Content (manual).** No automation reliably checks whether a cited source actually says what the prose claims. Every load-bearing inline citation — direct quotations, paraphrases attached to a page reference, "Habermas argues X"-style attributions — has to be spot-checked against the source by a human reader. The verification skill emits a worklist of these for human triage; it does not claim to resolve them.
3. **Context (manual).** Citations should be weighted appropriately: a peer-reviewed article in a recognised venue carries different epistemic weight than a conference paper, a blog post, or an entry in a contested-coverage database. Where a citation is being deployed in a load-bearing role, the doc should make the source's status legible (peer-reviewed journal, scholarly edition, encyclopedia entry, polemic) rather than presenting all citations as functionally equivalent.
4. **Famous misattributions (lint).** A separate denylist of known apocryphal quotations and misattributions — Voltaire-via-Hall, Smith's "invisible hand of the market" misuses, Burke's "all that is necessary…", Mill-on-bad-men, Tocqueville-on-America, the Einstein quote spectrum — is maintained as a lint pass. Matches require an explicit `misattributed` flag inline.

The combination of these layers does not produce certainty. It does drive the residual risk close to zero for the catastrophic class (cited works that do not exist; quotes attributed to authors who never wrote them) and converts the second-order class (slightly wrong page numbers, mis-remembered translation years, stale section references) into a normal copy-edit problem rather than a credibility-destroying one.

For the librarian's-eye-view perspective on AI-citation hallucinations that informed this policy, see Hana Lee Goldin's "[How to Spot AI Hallucinations Like a Reference Librarian](https://cardcatalogforlife.substack.com/p/how-to-spot-ai-hallucinations-like)" — particularly the three-layer existence/content/context model adapted above. The empirical-research-specific heuristics there (suspiciously specific decimals, impossible longitudinal timelines) do not translate to philosophy citations and are not used here.

The concrete working file as the policy is being bootstrapped is [`citation-inventory.md`](citation-inventory.md), which lists every work currently cited across `docs/` with a confidence rating per item. That file is the input to the canonical bibliography that will replace it.

## A live test case for the project's own framework

The project's two evaluation tests can be turned on the methodology itself:

- **The techno-feudal test.** Using an AI as a research mediator is a power-laden infrastructural relation. The model is a kind of lord — it filters the sources, the user depends on its filtering, the relationship is opaque and revocable. Simultaneously, AI is a *Diderot/Encyclopédie* move — knowledge becoming accessible to those locked out by the prerequisite of years of guided reading. Both readings are true. The project's honest answer is to be transparent about the dependency and to widen the path for readers to verify or contest the output.
- **The Habermasian test.** AI-mediated research that publishes its conclusions as authoritative is *strategic* communication — performing expertise. AI-mediated research that publishes its conclusions alongside its method, its failure modes, and an open invitation to correction is closer to *communicative* action — orientation toward mutual understanding and verifiability.

The methodology will not pass either test cleanly. Naming where it fails is part of the method.

## Invitation

Issues, pull requests, and notes from readers with deeper philosophical training than the author are explicitly invited. The most useful contributions:

- Corrections to misreadings or misattributions.
- Pointers to literature that complicates or contests claims made here.
- Disagreements with the syntheses where they paper over real positions.
- Suggested primary-text passages the author should read directly to ground a particular argument.

This is one of the things being open-source is *for*.
