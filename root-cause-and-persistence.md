# Root cause and persistence

## Root cause

The original `openchamber_web` main description and action schema are bundled inside the OpenChamber app:

```text
node_modules/@openchamber/web/server/lib/agent-tool/runtime.js:250
  (WEB_TOOL_DESCRIPTION, inside the app bundle / app.asar)

node_modules/@openchamber/web/server/lib/openchamber-control/actions.js:62-75
  (action titles/descriptions)
```

On current main the same text is at `packages/web/server/lib/agent-tool/runtime.js:250` and `packages/web/server/lib/openchamber-control/actions.js:62-75`.

The desktop app materializes the `agent-tool` source/run copies from this bundled template. Editing those copies can change behavior for the current install, but it is not a persistent fix: app regeneration/update can recreate them from the bundle. No app bundle was modified for this evidence.

The original text frames the page as an in-app browser panel / live session, which biases the model toward an embedded preview instead of a standalone page.

## Evidence

| Run | Final size | h1 | buttons | pause | Notes |
|---|---:|---:|---:|---:|---|
| original `openchamber_web` | 25,777 B | 0 | 0 | 0 | embedded preview, no standalone UI |
| no web + shell/Chrome | 51,702 B | 1 | 7 | 43 | standalone page |
| hybrid source patch, fresh session | **44,835 B** | **1** | **1** | **9** | recovered page UI, current install |
| DSH reference | 32,661 B | 1 | 6 | 23 | different agent, directional reference |

The successful page and screenshot are in this evidence repository:

- `success-after-hybrid.html`
- `success-after-hybrid.png`

## Temporary workaround

1. Edit the live managed source copy of the `openchamber_web` description/action descriptions to the hybrid positive text.
2. Restart the OpenCode service.
3. Use a new session.

This works for the current install, but it is not persistent: app updates/regeneration can restore the bundled template.

For a local persistent workaround, patch the bundled `@openchamber/web` template and re-sign the app. That is not recommended for general users because it modifies the signed app and is overwritten by updates.

## Why this should be fixed upstream

- The A/B held model, prompt, and settings constant; the original framing was associated with missing page UI, and the hybrid text restored it in the tested session. This is a tool-affordance/correctness issue, not merely a prompt or model-capability issue. Cross-model behavior was not measured.
- The current workaround requires editing app-internal copies; users should not need to patch app bundles or run scripts after updates.
- The tested fix is a description/action-schema change in the built-in template; the executor and browser panel behavior can stay unchanged.
- Keeping the tool for coding preview remains valid; the demonstrated scope is standalone HTML/creative tasks, and other task families were not measured.

## Proposed hybrid positive description

```text
A browser viewport for visual verification of a rendered page. Use one action per
call. Open the page, inspect its rendered text, controls, and errors, and capture
screenshots at desktop/mobile sizes. The viewport is a separate verification
surface; the page provides its own rendered content and behavior.
```

The action descriptions should use the same positive framing for `browser.open`, `browser.snapshot`, `browser.capture`, `browser.resize`, and `browser.close`. No page-content requirements should be added.
