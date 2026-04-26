# Tooling landscape

What's available for citation verification and where each tool's coverage breaks down. Coverage caveats are load-bearing — for Mündig's source roster (heavy on pre-DOI Enlightenment-era texts, German-language critical theory, French and English humanities monographs), most of the popular AI-citation-checking tools are weak in exactly the places it would matter.

The agent that surfaced most of this material flagged primary sources at the bottom of [`policy.md`](policy.md) and at [`../ai-assisted-research.md`](../ai-assisted-research.md).

## Open bibliographic databases

These are the candidates for the existence-check layer of the working policy ([`policy.md`](policy.md), Layer 1).

### CrossRef

- URL / API: <https://www.crossref.org/> · <https://api.crossref.org>
- Free, no key required.
- Strong on journal articles and post-2000 books with DOIs.
- Metadata is publisher-supplied and uncurated; author-name and edition slop are common.
- Coverage of pre-DOI humanities works (Kant, Locke, Smith, Mill, Spinoza, the Habermas pre-2000 corpus) is thin. Useless for the Mündig Enlightenment-era citations as a primary check; useful as a secondary check on the contemporary critical-theory entries.

### OpenAlex

- URL / API: <https://openalex.org/> · <https://api.openalex.org/>
- ~250M works indexed; positions itself as an open replacement for Scopus and Web of Science.
- "Significantly better coverage of non-English works and works from the Global South" — useful for the German *Strukturwandel*, the French Durand, the *Leviathan* Sonderband.
- Free tier with a documented `$1/day` ceiling (paid plans for higher limits). For one-off project verification at our scale, the free tier is sufficient.
- Documented limitations: ~40% of records lack abstracts; document-type errors are frequent; humanities publishers (Brill, CAIRN, OpenEdition) are under-indexed (cf. *arXiv:2512.16434*).
- The right choice as the *broad* secondary check after CrossRef and before falling through to canonical-edition URLs.

### OpenLibrary

- URL / API: <https://openlibrary.org/> · <https://openlibrary.org/developers/api>
- Free, no key required.
- ISBN/OLID lookup; weaker authority control than WorldCat but works as a fallback.

### OCLC WorldCat

- URL / API: <https://www.worldcat.org/> · <https://www.oclc.org/developer/api/oclc-apis/worldcat-search-api.en.html>
- Authoritative for editions and translations of older books. The only one of these that reliably distinguishes the Glasgow edition of *Wealth of Nations* from a 1937 reprint, or the Burger 1989 MIT Press translation of *Strukturwandel* from a different edition.
- Requires an API key.

### National library catalogues

- Library of Congress (US): <https://www.loc.gov/>
- Deutsche Nationalbibliothek (Germany): <https://www.dnb.de/> — canonical for Habermas and other German-language sources.
- Bibliothèque nationale de France: <https://www.bnf.fr/> — canonical for French sources (Durand, Foucault, etc.).
- All offer SRU/Z39.50 endpoints for programmatic lookup.
- These are the right authority for pre-DOI primary sources in their respective languages.

### Semantic Scholar

- URL / API: <https://www.semanticscholar.org/> · <https://api.semanticscholar.org/>
- Useful for citation-graph queries; weak coverage of monographs.
- Not a primary tool for our case.

## Canonical-edition full-text repositories

For pre-DOI sources, the right primary verifier is a full-text URL into a recognised digital archive that hosts a canonical edition. These are the URLs that should appear in the `url:` field of a `sources.json` entry when the work has no DOI.

- **HathiTrust** — <https://www.hathitrust.org/> — full-text search interface, identifier-keyed bibliographic API. Strong for older monographs.
- **Internet Archive** — <https://archive.org/> — broad coverage, less authoritative metadata than HathiTrust but often easier full-text access. Has an API.
- **Liberty Fund's Online Library of Liberty (OLL)** — <https://oll.libertyfund.org/> — reliable canonical-edition host for Smith (Glasgow Edition), Locke, Mill (Toronto *Collected Works*), and other Enlightenment-era texts. Has stable URLs.
- **Stanford Encyclopedia of Philosophy (archived)** — <https://plato.stanford.edu/> — for SEP entries, the citation policy is to link the *archived* snapshot at `plato.stanford.edu/archives/<date>/entries/<entry>`, not the live URL, because SEP entries are revised and citation needs to anchor to a fixed version. SEP itself recommends this practice.
- **PhilPapers** — <https://philpapers.org/> — discipline-specific authority for philosophy citations; deduplicates editions and translations under a single record. Has a documented public API.
- **Project Gutenberg** — <https://www.gutenberg.org/> — useful for primary-source full-text but editions are not always scholarly-canonical.
- **Google Books "search inside"** — no stable API, but the fastest way for a human to verify a specific quote against a specific edition. Cite as a finding aid that points to the print source, not as the source itself.

## Philosophy-specific scholarly editions

For the named thinkers in this project, citations should target canonical scholarly editions and use those editions' internal numbering (volume, section, paragraph) rather than reprint pagination. This is the *edition discipline* referenced in [`policy.md`](policy.md) and adopted from standard practice in academic philosophy.

| Thinker | Canonical edition | Citation form | Where hosted |
|---|---|---|---|
| Kant | Akademie-Ausgabe (Berlin / De Gruyter, vols I–XXIII) | `Ak. V:35` | Online text at <https://korpora.zim.uni-duisburg-essen.de/Kant/> and Internet Archive |
| Kant (English) | Cambridge Edition of the Works of Immanuel Kant | Same `Ak.` numbering, Cambridge translation noted | <https://www.cambridge.org/core/series/cambridge-edition-of-the-works-of-immanuel-kant/703660AAB7838A41309D7E80AD5C8EEE> |
| Smith | Glasgow Edition of the Works and Correspondence of Adam Smith (OUP / Liberty Fund) | `WN IV.ii.9` (book.chapter.paragraph) | <https://www.libertyfund.org/books/the-glasgow-edition-of-the-works-and-correspondence-of-adam-smith/> |
| Locke | Clarendon Edition of the Works of John Locke (OUP) | Section / paragraph references | Older Liberty Fund hosting at <https://oll.libertyfund.org/titles/locke-the-works-of-john-locke-in-nine-volumes>; Clarendon is canonical for new work |
| Mill | Collected Works of John Stuart Mill (Toronto / Routledge) | `CW XVIII:223` | Liberty Fund hosts the full text at <https://oll.libertyfund.org/> |
| Spinoza | Curley translation (Princeton) for English; Gebhardt for original | Curley page / Gebhardt section | Standard scholarly practice |
| Habermas | Suhrkamp originals; McCarthy translation (Beacon) for *TCA*; Rehg translation (MIT) for *Faktizität und Geltung* / *Between Facts and Norms* | Page reference to the cited edition, with edition stated | PhilPapers and SEP both reliably index these |

The edition discipline keeps the project's citations stable across reprints (a Penguin Classics paperback of *Wealth of Nations* will renumber pages every printing; the Glasgow `IV.ii.9` reference is permanent).

## Citation-management ecosystem

### Zotero with Better BibTeX / Better CSL JSON

- URLs: <https://www.zotero.org/> · <https://retorque.re/zotero-better-bibtex/exporting/pandoc/index.html>
- Citation manager with browser-and-PDF integrations, exports CSL JSON with stable citekeys via the Better BibTeX extension.
- "Keep updated" mode auto-rewrites the exported file as the Zotero library changes.
- *Important caveat*: Zotero's translators perform *retrieval*, not *verification* — they trust the metadata of the page they are scraping. A Zotero entry pulled from a publisher page is only as reliable as that page; an entry created by hand is only as reliable as the typist. Zotero is a useful workflow tool, not a verification layer.

### CSL JSON (Citation Style Language)

- The de-facto standard machine format for bibliographic entries. Pandoc, Zotero, and most academic toolchains speak it natively.
- Going via BibTeX as an intermediate format loses CSL-specific fields; CSL JSON or CSL YAML is the right canonical form.
- Reference: <https://docs.citationstyles.org/en/stable/specification.html>

### Pandoc citeproc

- URL: <https://pandoc.org/> (with `--citeproc`)
- Resolves `@citekey` references in Markdown against a bibliography file at build time. An unresolved citekey is a build failure — this is the build-gate mechanism referenced in [`policy.md`](policy.md), and it is free.

## LLM-citation-verification tools

A new class of tools has emerged specifically for catching hallucinated citations from AI-generated text. All of them are weak on the same axis: pre-DOI primary sources and humanities monographs (i.e., precisely Mündig's case).

- **AiCitationChecker** — <https://aicitationchecker.org/>
- **SwanRef** — <https://www.swanref.org/>
- **GPTZero hallucination detector** — <https://gptzero.me/hallucination-detector>
- **scite.ai Reference Check** — <https://scite.ai/blog/reference-check-an-easy-way-to-check-the-reliability-of-your-references-b2afcd64abc6>
- **Trinka Citation Checker** — <https://www.trinka.ai/features/citation-checker>

What they do well: verify journal articles via CrossRef + OpenAlex + Semantic Scholar; flag missing-DOI cases; surface metadata mismatches.

What they do not do well: verify pre-DOI works; verify quotation wording; verify page references; verify whether the cited source supports the claim. For a STEM paper with a 30-item reference list of post-2010 journal articles, these tools approach high confidence. For a humanities project with translations, scholarly editions, and 18th-century primary sources, they are useful as one signal among several but should not be relied on as a sufficient check.

For broader context on the scale of the problem, *Nature* has reported that ~2.6% of 2025 papers contain at least one hallucinated cite, with the AI-conferences corpus (NeurIPS) particularly affected (cf. <https://www.nature.com/articles/d41586-026-00969-z>).

## Librarian-side techniques

Hana Lee Goldin's "How to Spot AI Hallucinations Like a Reference Librarian" (<https://cardcatalogforlife.substack.com/p/how-to-spot-ai-hallucinations-like>) provides a usable three-layer model that the project's policy adopts:

- **Layer 1 — Existence**: Google the exact title in quotation marks, check the journal's website directly, search for the author paired with their stated institution.
- **Layer 2 — Content**: confirm the source actually says what the AI claims it says. Goldin notes that AI commonly inverts a real source's conclusion.
- **Layer 3 — Context**: evaluate whether the citation is being used appropriately — peer-reviewed journal vs. conference paper vs. blog post.

The empirical-research-specific heuristics Goldin also offers (suspiciously specific decimals, impossible longitudinal timelines, implausibly large sample sizes) are calibrated for STEM hallucinations and do not translate to philosophy citations. The Mündig failure-mode profile is wrong-page / wrong-edition / famous-misattribution, not fake percentages.

## What we are likely to use

The current shape (subject to revision):

- **Layer 1 (existence)** — DOI → CrossRef; ISBN → OpenLibrary or WorldCat; otherwise → OpenAlex; otherwise → a canonical-edition URL (HathiTrust / IA / OLL / SEP-archive / PhilPapers / national library) recorded in the `url:` field of `sources.json`.
- **Layer 2 (content)** — manual, via worklist emitted by the skill.
- **Layer 3 (context)** — manual, with the rule that citations of contested-coverage sources (encyclopaedia entries, blog posts, preprints) be marked as such inline.
- **Layer 4 (phrase-denylist + agent attentiveness)** — `bibcheck phrases` runs project-supplied denylist patterns as a separate pass (no curated baseline shipped); the `reference-librarian` agent catches famous misattributions from its own domain knowledge.

The bibliography is held in CSL JSON; the build runs through `pandoc --citeproc` so unresolved keys fail. Zotero with Better BibTeX is the likely management front-end.

This is a sketch, not a settled architecture. Open questions are documented at the end of [`policy.md`](policy.md).
