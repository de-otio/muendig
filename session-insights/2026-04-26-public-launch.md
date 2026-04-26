# 2026-04-26 — Public launch + Pages setup

## Current State

- Repo went public for the first time today; first commit ever pushed (`84d68f1`).
- Pages live at <https://de-otio.github.io/muendig/> — `main:/docs`, Cayman theme, `jekyll-readme-index` plugin makes `docs/README.md` the index.
- Branch protection on `main`: linear history required, force-push blocked, deletion blocked, **admin bypass on** (intentional — solo maintainer; protections guard against accidents, not against deliberate admin pushes).

## Gotchas Discovered

- **Org-level fork policy.** The de-otio org blocks forking on *all* repos. The GitHub error message is misleading — it says "This organization does not allow private repository forking" even when patching `allow_forking: true` on a *public* repo. The toggle is at <https://github.com/organizations/de-otio/settings/member_privileges> (Repository forking section). Until that's flipped at the org level, no one can fork the repo via standard GitHub flow.
- **Pages root ≠ repo root.** Pages serves `/docs` as the site root, so `../README.md` from inside `docs/*` resolves above the Pages site and 404s. We replaced cross-tree links from `docs/*` to root files (README, CLAUDE) with absolute `https://github.com/de-otio/muendig/blob/main/...` URLs. *Within* docs they stay relative.
- **`jekyll-readme-index` plugin.** Makes `docs/README.md` the Pages index, so we don't need a duplicate `index.md`. Listed in `docs/_config.yml` under `plugins:`.
- **Git author email.** First commit defaulted to user's `@jambit.com` work email even though [CLAUDE.md](../CLAUDE.md) declares `richard.myers@de-otio.org` is the project email. The initial commit was amended via per-command env vars (no git config changes — agents must not touch git config). User needs to set `git config user.email richard.myers@de-otio.org` locally for future commits in this repo. **This will recur** if a future agent commits before user has set the local config.
- **`gh repo edit --visibility`.** Hangs without `--accept-visibility-change-consequences` since it tries to open an interactive prompt.
- **`private-vulnerability-reporting` PUT** returns 404 on private repos but works on public. Worth retrying as part of any post-public-flip configuration sweep, not just on the initial pass.

## Patterns & Conventions Established

- **Docs folder is `docs/`** (renamed from `doc/` for Jekyll Pages compatibility). All path refs in root markdown use `docs/`.
- **Cross-tree links from `docs/*` → root markdown** use absolute github.com URLs. Within-docs links stay relative.
- **Per-component licensing.** Framework in [docs/licensing.md](../docs/licensing.md). No `LICENSE` file, no `package.json` license field, no licence headers until the first component takes shape and its licence has been decided per the framework. Default leanings: AGPLv3 for runtime / reference impls, Apache 2.0 for pure specs.
- **AI-assisted research disclosure** is in [docs/ai-assisted-research.md](../docs/ai-assisted-research.md) and surfaced in both README and CONTRIBUTING. Five named failure modes; each has a corresponding rule.
- **Citation discipline + plurality + hedging** are now project-level rules (memory-backed). When writing/editing docs: every philosophical claim needs work + year + locator; preserve genuine disagreements rather than synthesising; match prose confidence to claim confidence; mark interpretive claims as interpretive.
- **CLAUDE author/co-author trailer** is allowed and used: `Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>`.

## Known Pre-existing Issues

- **Fork policy at org level** — blocks open-source contribution flow. Listed for the user; needs human toggling at <https://github.com/organizations/de-otio/settings/member_privileges>.
- **No custom Pages domain.** Anti-feudal hygiene concern (the project critiques platform dependence; serving the docs from `de-otio.github.io` ties accessibility to GitHub). User aware; possible future setup uses `muendig.de-otio.org` via CNAME on `de-otio.org` → `de-otio.github.io`.
- **`docs/README.md` orientation map** was extended this session to include `ai-assisted-research.md` and `licensing.md` (was incomplete before). Future top-level additions to `docs/` should also go in the map.
- **Some `security_and_analysis` sub-features remain disabled** intentionally: `secret_scanning_non_provider_patterns`, `secret_scanning_ai_detection`, `secret_scanning_validity_checks`, delegated dismissal/bypass. These are advanced opt-in features; default-off is fine for a docs-first repo.
