# Changelog HTML — format & regeneration rules

`docs/changelog.html` is a **generated, static, offline-readable rendering of the entire
`docs/changelog.md`** — every version, every entry, newest first — styled as a browsable
"news blog" instead of a raw markdown file. It exists so anyone (including someone with no
git/markdown tooling open) can double-click the file and read the full shipped history in a
browser, no server or network required beyond the two Google Fonts requests (which degrade
gracefully to the fallback stack offline).

**It is generated, never hand-edited.** Treat it exactly like a build artifact: the source
of truth is `docs/changelog.md`.

## The rule

> **Every time `docs/changelog.md` changes, regenerate `docs/changelog.html` in the same
> commit/PR.** A changelog update without a regenerated HTML file is an incomplete doc
> update, the same as a stale `feature-spec.md`.

Run:
```bash
python3 scripts/generate_changelog_html.py
```
No dependencies beyond the Python 3 standard library. To check whether it's already
up to date (e.g. in CI, or before claiming a doc update is complete) without writing the
file:
```bash
python3 scripts/generate_changelog_html.py --check
```
Exits non-zero and prints what to run if `docs/changelog.html` is stale relative to
`docs/changelog.md`.

## What the generator does

Reads `docs/changelog.md` top to bottom and, for each `## [version] - date` (or
`## [Unreleased]`) block, collects every bullet under its `### Added` / `### Changed` /
`### Fixed` / `### Deprecated` sub-sections (merging repeated same-name sections within one
version — the source file sometimes has more than one `### Changed` block per version, and
that's fine; the generator doesn't require the markdown to be tidied first). Nested bullets
(the `  - **Backend.** …` sub-points under a top-level change) render as an indented list
under their parent. Inline markdown — `**bold**`, `` `code` ``, `[text](url)` — converts to
the equivalent HTML; a link keeps its original relative path (e.g.
`./features/24-profile-builder/feature-spec.md`), so it's correct when the page is served
alongside the rest of `/docs` but will show raw markdown if that target is opened directly by
a browser with no renderer — this is expected, not a bug to fix.

If you ever need to touch the generator itself (`scripts/generate_changelog_html.py`): the
trickiest part is that changelog entries mix `**bold**` tight against `` `code` `` (e.g.
`` **`k1l2m3n4o5p6`** ``) and use a literal `*` *inside* some code spans (e.g.
`` `workflow_action_*` `` as a wildcard prefix). Code spans are pulled out into placeholders
**before** bold/italic conversion runs and restored after, specifically so neither of those
cases corrupts the surrounding text. If you change the inline-formatting logic, re-run the
generator against the real `docs/changelog.md` afterward and grep the output for stray `**`
outside any `<code>` tag — that's the regression signature for this bug class.

## Visual system — identical to `skills/repo-pulse`'s reporting artifacts

This page intentionally shares its palette and type pairing with the ad-hoc "catch me up"
Artifacts produced by the `repo-pulse` skill, so the two read as one visual family instead of
two unrelated one-offs:

- **Type:** `Cormorant Garamond` (600) for headings, `Inter` for body/UI text, `IBM Plex Mono`
  for dates, version numbers, file paths, and anything tabular/technical. Same Google Fonts
  `@import` line as the repo-pulse template.
- **Color:** a warm parchment ground (`#f6f4ee` light / `#15140f` dark) — never pure white or
  pure black — with a deep teal accent (`#2f6e62` light / `#6cbca8` dark). Semantic category
  colors are distinct from the accent: `Added` green, `Changed` blue, `Fixed` amber,
  `Deprecated` brick red — each with its own soft background for the pill/rail. Both a light
  and a dark palette are defined as CSS custom properties on `:root`, redefined under
  `prefers-color-scheme: dark` and again under `:root[data-theme="dark"]`, per the artifact
  dark-mode pattern (an explicit theme choice must win over the OS default in both
  directions).
- **Layout:** single column, ~900px max width, one card per version (newest first), a sticky
  top toolbar with a version jump-nav and a plain-text client-side filter input (vanilla JS,
  no build step, works fully offline — it only filters what's already in the page, no
  network call). Each version card left-borders its category blocks in that category's color
  and pills the category name at top; entries render as a normal nested bullet list, not a
  card-per-bullet grid — with hundreds of entries across the project's history, a list scans
  faster than a card grid would.
- Full CSS lives inline in the generator's `TEMPLATE` string
  (`scripts/generate_changelog_html.py`) — copy tokens from there if you need to build a
  companion static page in the same family; don't redescribe the palette from memory.

## Relationship to `repo-pulse`

`repo-pulse` (see `skills/repo-pulse/SKILL.md` in this repo) produces an **ad-hoc, ephemeral**
snapshot for a conversation — "what's shipped, what's in flight right now, what's on the
radar" — published as a one-off Claude Artifact when the user asks to be caught up. This
changelog HTML is the opposite: a **permanent, comprehensive, repo-committed** record of
everything ever shipped, kept in lockstep with `changelog.md` as a normal part of shipping a
change. Don't use one to avoid maintaining the other — `repo-pulse`'s "what's shipped"
section may point here for full history instead of re-listing it, but the "right now" and
"on the radar" reporting it does has no equivalent in a changelog and still needs to be
produced fresh each time it's asked for.
