# Sibling repos (as of 2026-11-21)

A starting point for step 1 of the procedure, not a substitute for it. Check each line
against the repo before acting on it.

All the siblings except skkg-mcp build their committed `.skill` bundles with a
deterministic `scripts/build-skill.mjs`: stored zip entries, timestamps taken from
SKILL.md's `metadata.last_updated`, and SKILL.md's `metadata.version` synced from
`package.json`. Extend that script; don't add a second generator. The rijksmuseum,
iconclass and globalise scripts have a `--check` mode; odeuropa checks drift in
`test/skill.test.js` instead.

## odeuropa-eokg-mcp (done; the reference)

- `plugin/odeuropa-eokg/`, connector key `Odeuropa-EOKG`.
- `build:skill` writes the plugin's skill copy and both manifest versions.
  `test/skill.test.js` holds the drift checks.
- Pushes that touch `src/`, `package.json` or `package-lock.json` redeploy Railway;
  `plugin/` does not.

## rijksmuseum-mcp-plus

- TypeScript, built to `dist/`. Skill at `skill/rijksmuseum-mcp-plus/` (with `references/`).
  `build:skill` has `--check`.
- The repo also has `.agents/skills/rijksmuseum-mcp-plus/` and `skills-lock.json`. Find out
  what they are (a mirror, or an installed copy) before deciding which folder is the source.
- Watch patterns: `src/**`, `apps/**`, `package.json`, `package-lock.json`,
  `tsconfig.json`. A root `plugin/` is outside them.
- Has a viewer, so the README keeps the direct routes for it (see `platforms.md`).

## rijksmuseum-iconclass-mcp

- TypeScript. Skill at `skill/iconclass-mcp/` (SKILL.md only). The skill name differs from
  the repo name, so pick the plugin name deliberately.
- The bundle copy is named `.skill.zip`, not `.zip`.
- Watch patterns: `src/**`, `package.json`, `package-lock.json`, `tsconfig.json`.
- CI runs on every push and the push to `main` deploys.

## globalise-mcp (monorepo)

- The server package is `globalise-mcp-server/`. Skill at
  `globalise-mcp-server/skill/globalise-voc-research/`, built by
  `globalise-mcp-server/scripts/build-skill.mjs`. It also builds an `.mcpb`.
- Watch patterns include `/globalise-mcp-server/scripts/**` and
  `/globalise-mcp-server/*.json`, as well as `src/**`, `apps/**` and `data/**`.
  **So editing the build script redeploys.** Bundle that edit with work that may deploy,
  or tell the user.
- Where to put the plugin folder: at `globalise-mcp-server/plugin/<plugin>/` (path in the
  catalog: `globalise-mcp-server/plugin/<plugin>`) or at the repo root `plugin/<plugin>/`.
  Either is outside the watched patterns, since `*.json` matches only the top level. Ask
  the user which.
- There is a beta deployment as well as production. Use the production URL.
- `.agents/skills/agent-plugin-packaging/` is an older, gitignored, OpenAI-only skill on
  the same topic. Where they differ, this skill takes precedence.
- Has a viewer.

## skkg-mcp

- Plain ESM. Skill at `skill/skkg/` (SKILL.md only). There is **no `build:skill` script**
  yet: add one in the style of the others, with tests.
- `plugin/` is **gitignored** and holds a Codex-only experiment from 2026-11-08
  (`plugin/skkg-collection/`, with `.codex-plugin/plugin.json`, key `skkg`, a `.mcp.json`
  of type `streamable-http` (Claude needs `http`) with `headers: {}`, an icon, and its own
  version 0.1.3). A `git-subdir` entry fetches nothing
  from an ignored folder:
  - Remove the ignore rule and replace the folder with the layout in `files.md`.
  - Delete `.codex-plugin/` and `headers`.
  - Use `package.json`'s version.
  - Drop the personal email from `author`.
  - The leftover `assets/icon.svg` (a plain museum glyph, not SKKG's logo) can be
    reused as the icon, if the user agrees (see `files.md` § Icon).
  - Ask the user about the plugin name (`skkg-collection` or something else) and the key.
    The existing claude.ai connector is called "SKKG Sammlung".
- The skill's `compatibility` line asks for a local database file (`SKKG_DB_PATH`). That
  is false for the hosted connector, so reword it to cover both.
- Watch patterns: `src/**`, `package.json`, `package-lock.json`. Adding `build:skill` to
  `package.json` therefore redeploys.
- The skill is a short pointer to `skkg_guide`, and the README tells users to zip the
  folder themselves. Whether to add a `.skill` bundle is the user's call.
- `test/CLAUDE.md` sets the test rules (mutation entries for new assertions); read it
  before adding the drift checks.
- `CLAUDE.md` says `plugin/` is gitignored with its own version, and its release steps
  stamp the skill version by hand. Both change with this work.
- Has a viewer.

## Not yet: books-as-data

Not to be added to the marketplace for now.
