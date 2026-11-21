---
name: kintopp-plugin-packaging
description: Package a remote MCP server and its skill as a plugin for Claude (claude.ai, Claude Desktop, Claude Code) and ChatGPT (desktop app, Codex), and list it in the public kintopp/kintopp-plugins marketplace. Use when working in one of the sibling MCP server repos (rijksmuseum-mcp-plus, rijksmuseum-iconclass-mcp, globalise-mcp, skkg-mcp, odeuropa-eokg-mcp) and the task is to add a plugin/ folder, write .claude-plugin/plugin.json, plugin.json, .mcp.json or mcp.json, add a marketplace entry, sync the plugin's skill copy and version, raise a plugin version so installs update, explain how users install the plugin on either platform and its limits (free accounts, Codex mode only, chatgpt.com), or debug a plugin that installs but shows no skill or connector.
license: MIT
metadata:
  version: "0.1.0"
  last_updated: "2026-11-21"
---

# Packaging an MCP server as a kintopp-plugins plugin

A plugin here is a **remote MCP connector plus its research skill**, installed together.
Each plugin lives in its own public server repo, under `plugin/<name>/`. The marketplace
repo `kintopp/kintopp-plugins` holds only two catalog files that point at those
folders with `git-subdir` entries. `plugin/odeuropa-eokg/` in `kintopp/odeuropa-eokg-mcp`
is the working reference: read it and copy its shape, not its scripts.

The local clone of the marketplace is the directory that holds this skill (resolve the
symlink you loaded it through). Its `README.md` lists the plugins and the install steps.

## Scope

- Servers that are deployed over Streamable HTTP and need **no authentication**. If a
  server needs OAuth or an API key, stop and ask the user. The ChatGPT `mcp.json` schema
  rejects the auth key that OpenAI's docs show, and neither route has been tested.
- Packaging and listing only. Don't change server code, tools or the skill's content in
  the same piece of work, beyond the skill fixes named below.

## Procedure

1. **Inspect the repo; don't trust notes.** Versions, paths and watch patterns change.
   Find out:
   - where the hand-edited skill folder is (`skill/<name>/`, or deeper in a monorepo);
   - how it is built (`npm run build:skill`, and whether `--check` exists);
   - how tests run and where the skill tests live;
   - the hosted URL (README, `docs/deploy.md`);
   - Railway's `watchPatterns` in `.railway/railway.ts`, which decide what a push to
     `main` redeploys;
   - any existing `plugin/` folder and whether `.gitignore` excludes it;
   - the repo's own test rules (`test/CLAUDE.md`, mutation checks) and release steps,
     which the new checks and the build must follow.

   `references/repos.md` has what was true on 2026-11-21, as a starting point.
2. **Agree the names with the user** before writing files:
   - **Plugin name:** kebab-case, unique in the marketplace.
   - **Display name.**
   - **Connector key:** this is the connector's label. Claude shows it as written,
     ChatGPT capitalises only its first letter, and spaces are not allowed, so a form like
     `Odeuropa-EOKG` reads the same on both. Renaming it later makes every user reconnect.
   - **Author and website:** keep them as in the other entries.
   - **Description:** one or two sentences on what the user gets (the collection, the
     period, and "with a research guidance skill" only if the skill gives real guidance).
     The same text goes in both manifests and both catalog entries.
   - **Icon (optional):** whether the plugin gets one, and which image. See
     `references/files.md` § Icon. Don't use an institution's logo or branding without
     its permission: the servers are independent of the collections they serve.
3. **Write the plugin folder** from `references/files.md`:
   - two manifests;
   - two MCP configs with the same key and URL;
   - `skills/<skill-name>/`, a real copy of the skill;
   - the skill's `agents/openai.yaml`.
4. **Make the build own the copy.** Extend the repo's skill build, or add one if there is
   none, so that one command does two things:
   - (a) copies the whole skill folder recursively into
     `plugin/<name>/skills/<skill-name>/`;
   - (b) writes `package.json`'s version into both manifests.

   Put the drift checks wherever the repo already checks its skill, in `--check` or in
   the tests. Change the generated output only by re-running the build.

   Building a `.skill` bundle is not part of this task where the repo has none; ask the
   user. If the build also starts stamping SKILL.md's version, update the release steps
   that told a person to do it.
5. **Fix the skill where the plugin route makes it wrong.** A `compatibility` line that
   asks for a local database or stdio is false for a hosted connector. Fix that in the
   source skill.
   - **No hard-wrapped Markdown** in the skill folder: put each paragraph and each list
     item on one line. ChatGPT's skill page keeps line breaks inside a paragraph, so a
     wrapped SKILL.md shows as broken lines. Unwrap an existing skill, and add a test
     that fails on a wrapped paragraph.
   - **Don't invite users to edit the plugin's skill.** An update is expected to replace
     the installed copy, edits included (inferred: install caches are keyed by version). Users who want their own instructions add a second skill or
     put them in their project's instructions; the README can say so.
6. **Correct the repo's docs** that the change makes false: CLAUDE.md lines about
   `plugin/` or about versioning, release steps, and the README's install section. Then add
   the plugin route to the README, following `platforms.md`.
7. **Verify locally** (`references/verify.md`):
   - `claude plugin validate`;
   - schema checks of `plugin.json` and `mcp.json`;
   - the repo's tests and lint;
   - a Codex and Claude Code install from a local marketplace path, with a listing of the
     installed files.
8. **Add the catalog entries** in the marketplace clone, in **both**
   `.claude-plugin/marketplace.json` and `.agents/plugins/marketplace.json`, with the same
   `name` and source. Add the README row and the plugin's Claude Code install line.
   Templates are in `references/files.md`. Commit each repo separately, and keep catalog
   changes out of commits that touch this skill.
9. **Hand over the pushes.** Push order matters: the server repo first, so the
   `git-subdir` path exists on `main`, then the marketplace. Pushing is the user's call.
   Say which pushes redeploy Railway: **any** change to a watched file does, including
   adding a script to `package.json`, not only a version bump. After both pushes, refresh both CLIs and confirm the marketplace lists each
   plugin exactly once.
10. **Tell the user what to test in the apps** (`references/open-tests.md`), and keep any
   untested behaviour out of READMEs.

## Rules, and why

- **Installed copies update only when `version` changes.** Both platforms file their
  install cache by version, so edits pushed without a bump never reach existing users.
  The plugin version is `package.json`'s, written by the build. A bump redeploys the
  server, so fold it into a release.
- **Keep the plugin folder self-contained.** A `git-subdir` install fetches only that
  folder, so the skill must be real files there, not a symlink or a path outside it.
- **No top-level `bin/`** in the plugin folder; claude.ai refuses the plugin.
- **No `.mcp.json` at the repo root.** Claude Code would load it as the repo's own MCP
  config for anyone working in the repo.
- **Use only `git-subdir` (or `./`) sources.** Claude's `github` source type doesn't exist
  on the OpenAI side.
- **A pre-existing connector can make a plugin look installed.** Prove an install with
  something only the plugin supplies, usually the skill.
- **Grade every platform claim** as verified (with date and client version), inferred,
  or from docs only, and never pass the latter two off as verified. These surfaces change
  between minor releases.
- **The public repo holds no private details:** no home paths, personal email, private
  marketplaces or account plans.

## Reference map

- `references/platforms.md`: install routes on each platform, what works on free accounts,
  Codex-mode-only on ChatGPT, labels, updates, and the grade of each claim. Read it before
  writing install instructions.
- `references/files.md`: exact templates for the four plugin files, `openai.yaml`, both
  catalog entries, and the drift checks.
- `references/repos.md`: per-repo notes for the sibling servers as of 2026-11-21.
- `references/verify.md`: validator, schema, CLI install ladder, probe plugins.
- `references/open-tests.md`: what only the user can test in the apps, with steps and
  what to record.
