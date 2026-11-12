# Roadmap

Ordered by user-visible value. Items are sized for one focused change each.

## Now — make the fork a trustworthy baseline

- [x] Verify on DSH 0.2.2-rc.1 end to end: boot, stream a card, submit (update still to verify).
- [x] Fix the jsdom test suite (date-dependent calendar case).
- [ ] CI: install, build, test on every push; publish on tag (workflow added, release job pending).
- [ ] Pin `lib/client.js` size budget and fail the build if it grows > 10%.

## Next — protocol and streaming

- [ ] Accept A2UI v1.0 wire envelopes (`createSurface`, `updateComponents`,
  `updateDataModel`) in `a2ui_render`/`a2ui_update` while keeping the current
  flat document as a legacy input.
- [ ] Stream partial component trees: render a node as soon as its `id` and
  `component` are complete instead of waiting for the whole JSON argument.
- [ ] Surface validation diagnostics in the card chrome (what failed, which
  component id) instead of a generic parse error.

## Later — surface capabilities

- [ ] `a2ui_remove` for tearing down a surface (today only replace/update).
- [ ] Theme token bridge: document which `--dsw-*` tokens components consume so
  third-party catalogs can match the host.
- [ ] Catalog versioning: let the host advertise `catalogId` and reject
  documents authored against a different catalog.

## Non-goals

- Replacing the component catalog with agent-written HTML/TSX. The safety model
  (agent composes only registered components) is the reason this plugin exists.
- Headless rendering: the plugin is a Web client extension by design.
