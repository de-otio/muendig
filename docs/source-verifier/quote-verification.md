# Quote verification against primary-source corpora — deferred design

**Status: deferred. Considered, scoped, and documented; not currently planned for build.** This file captures the analysis so the option remains discoverable, and so a later decision to reopen it doesn't have to redo the research.

## The gap this would close

Layer 2 of the [working policy](policy.md) — content verification, *does the cited source actually say what the prose claims?* — is currently fully manual. [`bibcheck`](reference-librarian/README.md) emits a worklist of items needing human triage, and a human reviewer opens each cited source to confirm the wording. This is the deepest verification layer and the most time-consuming.

For pre-DOI primary sources (Locke, Smith, Mill, Rousseau, Voltaire, Wollstonecraft, Paine, Spinoza), open full-text repositories — Project Gutenberg, Internet Archive, Wikisource — host the actual texts. In principle a tool could fetch the relevant text, run fuzzy quote-matching against it, and return a verdict on whether the quoted wording appears in the source. This would convert a portion of Layer 2 from manual to automatic.

## What this would and would not buy

**Buys:**

- For the Enlightenment-lineage half of the Mündig corpus, automatable Layer 2 quote verification — the most expensive manual layer of the policy gets cheaper.
- A genuine commons-leveraging move: PG, IA, and Wikisource are exactly the open-knowledge infrastructure the project's framing favours. The Diderot-encyclopedist analogy in [`../techno-feudalism/enlightenment-lineage.md`](../techno-feudalism/enlightenment-lineage.md) is the relevant precedent — knowledge accessibility as anti-feudal infrastructure.
- Both Mündig evaluation tests favour it. Techno-feudal: builds on the open commons rather than enclosing. Habermasian: makes content verifiable, communicative-action-supporting.

**Does not buy:**

- **Page or section reference verification.** The available open editions are typically 19th–early-20th-century public-domain reprints and translations, not the canonical scholarly editions the project's edition-discipline rule prefers (Glasgow Smith, Akademie Kant, Toronto CW Mill, Clarendon Locke). A successful match against PG's reprint of Smith tells you *Smith wrote something like this* — **not** *the Glasgow `WN IV.ii.9` reference is correct*.
- **Coverage of the modern critical-theory corpus.** Habermas, Negt/Kluge, Fraser, Varoufakis, Durand, Wark, Morozov — all under copyright, none in PG or Wikisource. The argumentatively load-bearing citations in `docs/techno-feudalism/habermas/` get zero help. Internet Archive holds *some* in-copyright works under controlled-lending terms but those are not bulk-fetchable.
- **Coverage of the *canonical* scholarly editions for older authors.** This is where Internet Archive matters more than PG: IA hosts scans of Glasgow Smith, Cambridge Kant, Toronto Mill where those are out of copyright. OCR quality is variable but workable for fuzzy matching; the `ia` CLI is mature.

For Mündig specifically, this feature helps roughly the Enlightenment-lineage half of the corpus and barely touches the Habermas-and-after half.

## Project Gutenberg integration — what's actually allowed

Per the [Project Gutenberg robot access policy](https://www.gutenberg.org/policy/robot_access.html):

- The human-facing website (`www.gutenberg.org`) is **off-limits to automated browsing**. Hitting it programmatically triggers IP blocks.
- Sanctioned bulk-access endpoints:
  - `wget` against `https://www.gutenberg.org/robot/harvest?filetypes[]=...` with the recommended `-w 2` (2-second delay).
  - rsync against `aleph.gutenberg.org::gutenberg` for incremental, idempotent mirroring. This is the right path for ongoing sync.
  - Catalog data is published in machine-readable RDF/XML at the [offline catalogs page](https://www.gutenberg.org/ebooks/offline_catalogs.html).
- Available formats: `html`, `txt`, `epub`, `kindle`, `mp3`. Plain `.txt` is what you want for fuzzy matching.
- Files update frequently (hundreds of revisions monthly). Generated formats rebuild monthly.

So the technical envelope is fine. The constraint is on *how* you sync, not *whether* you can.

## Other primary-source repositories worth considering

The Project Gutenberg question is part of a broader landscape:

- **Internet Archive** ([archive.org](https://archive.org/)). Largest scan corpus including original Glasgow / Cambridge / Toronto editions where out of copyright. Mature [`internetarchive` Python library / `ia` CLI](https://github.com/jjjake/internetarchive). License is per-item (PD, CC, or controlled-lending). OCR quality variable but acceptable for fuzzy matching with cleanup. **For Mündig's named-philosopher canon, IA is more valuable than PG** because it has the canonical editions, not just the public-domain reprints.
- **Standard Ebooks** ([standardebooks.org](https://standardebooks.org/)). ~1,400 carefully re-edited PG books, CC0 editorial. Higher quality than PG; smaller catalogue. Bulk-download is paywalled but [github.com/standardebooks](https://github.com/standardebooks) hosts source XHTML per book in public repos.
- **Wikisource** ([wikisource.org](https://wikisource.org/)). Smaller than PG but each page links to a backing IA scan — strong provenance. XML dumps available at [dumps.wikimedia.org](https://dumps.wikimedia.org/).
- **HathiTrust** ([hathitrust.org](https://www.hathitrust.org/)). 17.1M volumes including scholarly editions PG lacks. Public-domain full text downloadable via institutional research-dataset request only. The publicly-downloadable [HTRC Extracted Features Dataset](https://analytics.hathitrust.org/datasets) is page-level token counts only, **not running text** — useless for quote matching.
- **Liberty Fund OLL** ([oll.libertyfund.org](https://oll.libertyfund.org/)). Curated Enlightenment political-philosophy canon (Glasgow Smith, Liberty-Fund Hume, Locke, Mill). **No bulk-download endpoint, no API, no GitHub mirror.** Per-title HTML/EPUB/PDF download links exist; ToU restricts redistribution. Not designed for programmatic access.
- **GITenberg** ([github.com/GITenberg](https://github.com/GITenberg)). PG mirrored as ~74,000 git repos (one per book) with metadata YAML. Awkward at scale; the [`gitberg`](https://github.com/gitenberg-dev/gitberg) CLI handles it.

For Mündig, the practical mix would likely be: rsync against PG for breadth, `ia` CLI for canonical-edition scans, Wikisource dumps for verified-edition provenance. Liberty Fund OLL stays manual for now.

## Quote-matching tools — what exists, what doesn't

The components for fuzzy quote-matching against a downloaded corpus exist; the integration into a CLI ergonomically usable from a citation-verification workflow does not.

- **Passim** ([github.com/dasmiq/passim](https://github.com/dasmiq/passim)). The serious academic text-reuse tool. Apache Spark-based, designed for "find this passage in a corpus." Used in the Programming Historian's [Detecting Text Reuse](https://programminghistorian.org/en/lessons/detecting-text-reuse-with-passim) tutorial. Heavyweight (Spark, JVM); right shape for "verify a worklist of N quotes against an M-volume corpus."
- **text-matcher** ([github.com/JonathanReeve/text-matcher](https://github.com/JonathanReeve/text-matcher)). Pure-Python CLI, n-gram based, designed for pairwise comparison. Simpler than Passim. Reusable matcher core.
- **RapidFuzz** ([github.com/rapidfuzz/RapidFuzz](https://github.com/rapidfuzz/RapidFuzz)). Library primitive, not a tool. `partial_ratio_alignment` finds best alignment of a short string in a long string and returns indices; `process.cdist()` for batch. The obvious building block for a DIY implementation.
- **SemanticCite** ([arXiv 2511.16198](https://arxiv.org/html/2511.16198v1), Nov 2025). Hybrid dense+BM25+rerank. Closest published thing to citation Layer 2 automation. Research-paper-stage; code-release status unclear.
- **PG sync tooling**: rsync against `aleph.gutenberg.org::gutenberg` is the official path; [`gutenbergpy`](https://pypi.org/project/gutenbergpy/) is the modern Python wrapper; [`pgcorpus/gutenberg`](https://github.com/pgcorpus/gutenberg) is a one-shot snapshot pipeline; [`gutenberg_cleaner`](https://github.com/kiasar/gutenberg_cleaner) strips PG header/footer.

**Edition-mismatch handling** is essentially unsolved for Enlightenment philosophy. The CTS / [CapiTainS / MyCapytain](https://github.com/capitains/MyCapytain) ecosystem addresses cross-edition lookup but only for Greek/Latin classics. For Smith / Kant / Mill / Locke specifically, there is no existing tool that maps "Glasgow `WN IV.ii.9`" to "this passage in PG's 1904 reprint of Cannan's edition." The pragmatic substitute is to fuzzy-match the *quoted prose* against any available edition, ignoring edition pagination, and to be honest in the UI that a positive match means "the author wrote these words" not "the page reference is correct."

## Where it should live (if built)

**Not inside `bibcheck`.** The narrow-scope decision documented in [`reference-librarian/README.md`](reference-librarian/README.md) is load-bearing. Adding quote-against-fulltext verification would change bibcheck's storage profile (gigabytes vs. cache-only), cadence (periodic sync vs. on-demand), and problem class (full-text fuzzy match vs. metadata API check). Putting it in bibcheck would broaden scope past the point the prior-art survey justified, and would entangle two tools whose release cadences and dependency profiles differ.

**As a sister tool**, working name `quotecheck` (or `primary-source-verifier`):

- Maintains a local mirror of selected open-corpus primary sources (PG via rsync, IA via `ia` for canonical-edition scans where available, Wikisource via XML dumps for proofread-against-scan provenance).
- Exposes a CLI: `quotecheck "verbatim quote" --work-id <CSL-citekey-or-PG-id>` → ranked fuzzy matches with confidence scores, edition explicitly named in the response.
- Honest output framing: "verified-author-wrote-these-words" not "verified-cited-page."
- Composable with bibcheck: a `quotecheck-callable: true` flag in bibcheck's worklist items for entries whose source is in a covered corpus, so the human triage step becomes "run quotecheck on these" rather than "open HathiTrust manually."
- Permissive licence; npm or pip distribution; same separate-repo logic as bibcheck.

This preserves bibcheck's narrow scope while making the feature available, and matches the per-component-licensing convention ([`../licensing.md`](../licensing.md)).

## Recommendation: defer

**Hold off on this for Mündig.** Reasons:

1. **Half the project's corpus is out of scope.** The Mündig source roster is roughly half pre-20th-century philosophy (where PG/IA help) and half modern critical theory (where they don't). Half the actual citation-verification work this would automate stays manual either way.
2. **Edition-mismatch caveat is real and load-bearing.** A "verified" indicator that means "the author wrote something like this in some edition" is weaker than the four-layer policy currently promises, and risks giving false confidence if not surfaced carefully. The framing question is non-trivial.
3. **bibcheck isn't built yet.** Building bibcheck first, seeing what worklist items actually accumulate in practice, and *then* deciding whether the sister tool is worth the lift is cheaper than designing both at once.
4. **Wheel-reinvention risk if a dedicated tool emerges.** Passim has been around since ~2014; SemanticCite is fresh; the academic text-reuse community is active. The right move if a usable open-source quote-verifier ships before Mündig needs one is to depend on it rather than build a new one.

## Reconsider when

The case to revisit this strengthens if any of the following obtain:

- **bibcheck v1 is in use for ~3 months** and the worklist is dominated by Enlightenment-era primary-source quotations that could be matched against PG / IA / Wikisource.
- **A new contributor to Mündig** has the bandwidth and interest to build a sister tool; the broader open-source community would benefit.
- **A usable open-source quote-verifier emerges** (e.g., SemanticCite ships code; Passim gets a thin CLI wrapper; someone builds a `quotecheck`-shaped CLI elsewhere). In that case the move is to depend on it, not build.
- **Mündig's source mix shifts** toward older philosophy and away from modern critical theory, increasing the fraction of the corpus this would help.
- **A specific incident** — a misquotation in the published docs that an open-corpus quote-checker would have caught — provides motivating concrete evidence.

If none of these obtains within 6–12 months of bibcheck v1, the right move is to keep this file as a record and let the open-source community catch up.

## References

- [Project Gutenberg robot access policy](https://www.gutenberg.org/policy/robot_access.html)
- [Project Gutenberg mirroring how-to](https://www.gutenberg.org/help/mirroring.html)
- [Internet Archive `internetarchive` Python lib / `ia` CLI](https://github.com/jjjake/internetarchive)
- [Standard Ebooks GitHub org](https://github.com/standardebooks)
- [Wikisource Database dumps](https://en.wikisource.org/wiki/Wikisource:Database_dump)
- [HathiTrust Datasets](https://www.hathitrust.org/datasets)
- [HTRC Extracted Features](https://analytics.hathitrust.org/datasets)
- [Liberty Fund Online Library of Liberty](https://oll.libertyfund.org/)
- [GITenberg org](https://github.com/GITenberg) / [gitenberg-dev](https://github.com/gitenberg-dev) / [gitberg CLI](https://github.com/gitenberg-dev/gitberg)
- [dasmiq/passim](https://github.com/dasmiq/passim) and [Programming Historian: Detecting Text Reuse with Passim](https://programminghistorian.org/en/lessons/detecting-text-reuse-with-passim)
- [JonathanReeve/text-matcher](https://github.com/JonathanReeve/text-matcher)
- [rapidfuzz/RapidFuzz](https://github.com/rapidfuzz/RapidFuzz) — `partial_ratio_alignment`
- [SemanticCite (arXiv 2511.16198, Nov 2025)](https://arxiv.org/html/2511.16198v1)
- [gutenbergpy](https://pypi.org/project/gutenbergpy/) / [pgcorpus/gutenberg](https://github.com/pgcorpus/gutenberg) / [gutenberg_cleaner](https://github.com/kiasar/gutenberg_cleaner)
- [CapiTainS / MyCapytain](https://github.com/capitains/MyCapytain) / [Canonical Text Services](https://wiki.digitalclassicist.org/Canonical_Text_Services)
