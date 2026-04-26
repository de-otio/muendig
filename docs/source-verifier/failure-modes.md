# Citation-hallucination failure modes

Categories of citation problems that AI-assisted research can introduce, ordered roughly by reputational risk. The five-failure-mode list in [`../ai-assisted-research.md`](../ai-assisted-research.md) is the project-wide framing; this file is the deeper dive on failure mode 3 ("Confabulated citations and quotations") specifically.

For each: what it is, an example, whether it is automatable to catch, and what part of the working policy ([`policy.md`](policy.md)) addresses it.

## 1. Fabricated work

A book or article that does not exist is cited as if it does — wrong author, wrong title, wrong year, all together, or a real-author-and-real-topic combination that looks plausible but never appeared in print.

- *Example*: AI invents "Habermas, *On Digital Publics*, MIT Press, 2019" — Habermas has no such book.
- *Automatable*: yes, mostly. Bibliographic databases (CrossRef, OpenAlex, OpenLibrary, WorldCat) will return no record. Coverage gaps in humanities and pre-DOI works mean a no-record result must be treated as inconclusive rather than dispositive — but it is a strong signal.
- *Highest reputational risk.* If a peer reads the docs and tries to look up a cited work that doesn't exist, the project's credibility is gone in one click.
- *Addressed by*: Layer 1 (existence) — every cited work has an entry in `sources.json` verified against a bibliographic database or a canonical-edition URL.

## 2. Real work, wrong attribution

A real book or article cited under the wrong author, or a real author cited as having written something they did not.

- *Example*: an essay actually by Geoff Eley cited as Habermas; or a real Habermas essay cited under a wrong title that conflates two distinct works.
- *Automatable*: yes. Bibliographic-database lookup catches it because the metadata mismatch is visible.
- *Addressed by*: Layer 1.

## 3. Real work, wrong year / wrong venue / wrong publisher

The work exists and is correctly attributed, but a peripheral metadata field is off — translation year, original year, journal volume, issue, publisher, edition.

- *Example*: Negt and Kluge cited as 1993 (year of the English translation) without the 1972 German original; or *Strukturwandel* cited as Suhrkamp 1962 (it was Luchterhand 1962; Suhrkamp later).
- *Automatable*: partially. Database metadata can confirm but it is itself sometimes inconsistent (publisher slop is documented for OpenAlex; CrossRef's metadata is publisher-supplied and uncurated). Translations vs. originals are a particular trouble spot — some databases conflate them, some duplicate them.
- *Addressed by*: Layer 1, with the caveat that database disagreements need human resolution.

## 4. Wrong page or section reference

The work and its metadata are correct, but the page or section the prose cites does not contain what the prose claims.

- *Example*: "Habermas, *Strukturwandel*, p. 217" used as the citation for a claim that appears on a different page (or in a different chapter) entirely.
- *Automatable*: no. Page-level verification requires reading the actual source. Full-text databases (HathiTrust, Internet Archive, Google Books) help a human verifier locate the passage but do not perform the check.
- *Addressed by*: Layer 2 (content) — manual spot-check via worklist.

## 5. Misquotation — real source, wrong wording

The cited source is correct but the quoted wording is invented, paraphrased while presented as a direct quotation, or stitched together from non-adjacent passages.

- *Example*: a Mill passage from *On Liberty* Ch. I given inside quote marks but with words rearranged or two distant phrases joined with an "and." Or a famous formulation attributed verbatim where the actual text is similar but distinct.
- *Automatable*: only weakly. Some full-text-search tools can locate exact strings but humanities works in older editions are unevenly digitised; OCR errors muddy the signal.
- *Addressed by*: Layer 2, with the additional rule that direct quotations default to paraphrase unless the user has the source open.

## 6. Misattribution of a real quote to the wrong source within an author's corpus

The quote is real, the attribution to the author is correct, but the cited *work* is wrong.

- *Example*: a Locke passage from *An Essay Concerning Human Understanding* cited as if from the *Second Treatise*. Both are real Locke; the cite is to the wrong one.
- *Automatable*: no in the general case. Some quote-search tools (Quote Investigator, Wikiquote, Project Gutenberg full-text) catch the most-cited cases but coverage is patchy.
- *Addressed by*: Layer 2.

## 7. Famous misattributions — real-sounding quote, attributed to a person who never said it

The quote circulates widely under a famous person's name but the attribution itself is apocryphal.

- *Examples*:
  - "I disapprove of what you say but will defend to the death your right to say it." — Voltaire? No, Evelyn Beatrice Hall in *The Friends of Voltaire* (1906), as a paraphrase of Voltaire's position. Already correctly handled in [`../techno-feudalism/enlightenment-lineage.md`](../techno-feudalism/enlightenment-lineage.md), which flags it explicitly.
  - "All that is necessary for the triumph of evil is that good men do nothing." — Burke? No, never appears in Burke's writings.
  - "America is great because she is good." — Tocqueville? No; widely attributed but not in *Democracy in America*.
  - "The market is governed by an invisible hand." — Smith? Smith uses the phrase three times across his entire corpus, all in different argumentative contexts; the modern free-market gloss is a later projection.
  - Most "Einstein said…", most "Mark Twain said…", most "Churchill said…" quotes.
- *Automatable*: partially. A regex denylist can flag prose containing a denylisted quote without an acknowledgement marker. **bibcheck does not ship a curated baseline** — its `bibcheck phrases` subcommand is a generic project-supplied lint, not a global famous-fakes registry, since any baseline shipped with a generic tool would be both incomplete (a tiny slice of the misattribution universe) and reputationally load-bearing (a single bad pattern would taint the rest). The full set of famous misattributions is therefore caught by the `reference-librarian` agent's domain knowledge, supplemented by Quote Investigator (https://quoteinvestigator.com/) for cases beyond the agent's training.
- *Highest reputational risk per occurrence* if missed in published critical-theory work, because these are exactly the failures that signal "this author did not check."
- *Addressed by*: Layer 4 — `reference-librarian` agent attentiveness (always; the agent's prompt enumerates ~12 canonical examples), plus an opt-in `bibcheck phrases` lint when the project supplies a denylist for project-specific patterns (style-guide deprecations, retracted-source wording).

## 8. Real source, claim not supported

The cited source exists, the page reference is accurate, the wording is correct — and the source does not actually support the prose's claim. Often the source argues the *opposite* of what is being cited for, or supports a much narrower claim than the prose deploys.

- *Example*: AI cites a real empirical paper as showing that filter bubbles are causally responsible for polarisation, when the paper actually shows the effect is small or absent (cf. Bruns 2019). The librarian-side reference in [`../ai-assisted-research.md`](../ai-assisted-research.md) flags this as Goldin's "Layer 2" failure mode.
- *Automatable*: no. This is the deepest manual-verification class. It requires reading the source carefully against the claim being made.
- *Addressed by*: Layer 2 (content), as a manual triage item.

## 9. Real source, weighted inappropriately

The cited source is real and accurate, but is being deployed with more authority than its publication context warrants — a blog post or conference paper used as if it were peer-reviewed work; a contested-coverage database entry treated as definitive.

- *Example*: citing a Wikipedia entry, a Substack post, or an LLM-generated summary as if it were a scholarly source.
- *Automatable*: partially. Source-type classification (peer-reviewed journal vs. preprint vs. blog vs. encyclopaedia entry) is metadata-derivable for some sources and not for others.
- *Addressed by*: Layer 3 (context) — make the source's status legible inline; require the prose to acknowledge what kind of source is being cited.

## What automation does and does not buy

Failure modes 1, 2, 3, and 7 are the bulk of what a `verify-citations` skill can catch automatically. They are also the failures that scale: an AI-drafted document can introduce dozens of them without any single one being elaborate. Automating the catch keeps the residual risk on the catastrophic class close to zero.

Failure modes 4, 5, 6, 8, and 9 require human eyes on a real source. There is no API that resolves them. The goal of the policy is to:

1. Eliminate the catastrophic class (1, 2, 7) by gate.
2. Reduce the second-order class (3) to a normal copy-edit problem.
3. Surface the manual-verification class (4, 5, 6, 8, 9) as a finite, prioritised worklist that a human can actually work through, rather than as an unbounded ambient anxiety.

This is the working theory. See [`policy.md`](policy.md) for the architecture proposed to do it.
