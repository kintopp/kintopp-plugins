# Sibling repos (as of 2026-11-21)

A starting point for step 1 of the procedure, not a substitute for it. Check each line
against the repo before acting on it.

All the siblings except skkg-mcp (which has no `.skill` bundle) build their committed `.skill` bundles with a
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

## rijksmuseum-iconclass-mcp (done; upload zips only, as of 2026-11-23)

- TypeScript. Skill at `skill/iconclass/` (SKILL.md and `agents/openai.yaml`; renamed from `iconclass-mcp` on 2026-11-23, since Claude labels a skill by its `name`). The skill name differs from
  the repo name, so pick the plugin name deliberately.
- The build writes `skill/iconclass.skill` and `skill/iconclass.zip`, which are also the release assets, plus the upload zips `plugin/rijksmuseum-iconclass-{claude,chatgpt}.zip`.
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

## skkg-mcp (done; upload zips only, as of 2026-11-23)

- `plugin/skkg/`, plugin name `skkg`, display name "SKKG Collection", connector key
  `SKKG`, skill display name "SKKG". **Not in the marketplace:** the user removed its
  entry on 2026-11-23. Users install it by uploading `plugin/skkg-claude.zip` or
  `plugin/skkg-chatgpt.zip`, and the README points at those zips. Ask the user before
  listing it again; the name `skkg` was published, so keep it.
- `scripts/build-skill.mjs` (`npm run build:skill`) copies `skill/skkg/` into the
  plugin, writes `package.json`'s version into both manifests, and packs the two zips
  byte-reproducibly (stored entries, date from SKILL.md's `metadata.last_updated`).
  `--check` only reports stale output. There is no `.skill` bundle.
- `test/skill.test.js` holds the drift checks, the exact file list of each zip, the
  57-character `shortDescription` limit, and a run of `--check`; each has an entry in
  `test/mutations.mjs`. `test/CLAUDE.md` sets the test rules.
- The skill is a short pointer to `skkg_guide`, one line per paragraph, with no notes
  section. Its `compatibility` line covers both the hosted connector and a local
  install.
- Watch patterns: `src/**`, `package.json`, `package-lock.json`. The build script,
  tests and `plugin/` are outside them, so changing those doesn't redeploy; a version
  bump does.
- The release steps in `CLAUDE.md` stamp SKILL.md's `version` and `last_updated` by
  hand, then run `npm run build:skill`.
- Uploading either zip has not been tested in the apps. A copy of `skkg` installed from
  the marketplace earlier can make an upload test look successful.
- Has a viewer.

## Not yet: books-as-data

Not to be added to the marketplace for now.
