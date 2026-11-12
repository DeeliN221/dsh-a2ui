# Maintenance

`dsh-a2ui` is a long-lived fork of
[baihui-ai/a2ui-render-in-dsh](https://github.com/baihui-ai/a2ui-render-in-dsh).
This file records how the fork is kept alive, what it guarantees, and how to
change it safely.

## Why a fork

Upstream `0.2.0` was published 2026-08-31 and targets DSH `>=0.1.1-rc.1`. DSH
moves fast (dev preview, breaking changes between minor releases), and the
plugin touches three unstable seams:

| Seam | Used for | Why it can break |
| --- | --- | --- |
| `@deepseek-ai/dsh-tools` `defineTool` | host tool registration (`a2ui_render`, `a2ui_update`, `a2ui_catalog`) | parameter spec / output contract changes |
| client slot `tool.call.toolview` | binding a React component to a tool call | slot name, registration signature, props |
| client `connection` API | sending a submission back as a user message | service shape / RPC names |

The fork's job is to keep those seams working and to evolve the surface
protocol, not to redesign the component catalog.

## Compatibility policy

- **Supported**: DSH `>=0.2.2-rc.1 <0.3.0`, Node `>=22.19`, Web profile only.
- **Verified**: every published version is installed and exercised against the
  exact DSH version in `package.json#engines.dsh` (see below), with a screenshot
  of a rendered card kept in the release notes.
- **Unsupported**: headless/CLI profiles, non-Web hosts. The client half is a
  browser bundle; there is nothing to render without the Web UI.

## Upstream sync

Upstream is kept as a remote, never merged blindly:

```sh
git remote add upstream https://github.com/baihui-ai/a2ui-render-in-dsh.git
git fetch upstream
git log --oneline main..upstream/main
```

Sync rules:

1. Take component-level fixes (`src/client/components*.jsx`, `styles.js`,
   `markdown.jsx`, `expr.js`) when they apply.
2. Never take upstream's DSH integration untouched — re-audit the three seams
   above against the current DSH release.
3. Keep upstream copyright and MIT license intact; add fork changes to
   `CHANGELOG.md` instead of rewriting history.

## Release checklist

1. `pnpm install && pnpm run build && pnpm test` — all green.
2. Link-install into a throwaway profile and confirm the plugin boots:
   `dsh plugin --profile a2ui-check add link:$PWD`
3. In a real Web UI session, prompt for a card and verify:
   streaming render, submit round-trip, in-place `a2ui_update`.
4. Bump `package.json` version and `CHANGELOG.md`.
5. `npm publish`, then `dsh plugin --profile web add dsh-a2ui` on a clean box.

## Known debt

- The test suite's todo-navigator section fails on jsdom `>=30.1` (an element
  lookup returns `undefined`); the rest of the suite passes. Tracked in
  `docs/ROADMAP.md`.
- The surface protocol currently follows A2UI v0.9 (flat component adjacency
  list). Migration to the v1.0 wire messages (`createSurface` /
  `updateComponents` / `updateDataModel`) is a roadmap item.
