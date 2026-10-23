Root-cause follow-up: the description is baked into the app bundle, and source/run patches are not persistent.

The original `openchamber_web` main description/action schema is bundled inside the desktop app:

```text
node_modules/@openchamber/web/server/lib/agent-tool/runtime.js:250
  (WEB_TOOL_DESCRIPTION, inside the app bundle / app.asar)

node_modules/@openchamber/web/server/lib/openchamber-control/actions.js:62-75
  (action titles/descriptions)
```

On current main the same text is at `packages/web/server/lib/agent-tool/runtime.js:250` and `packages/web/server/lib/openchamber-control/actions.js:62-75`.

The `agent-tool` source/run copies are derived artifacts. Editing those copies can change the current install, but app regeneration/updates can recreate them from the bundled template. That is why a file patch/script is a temporary workaround, not a persistent fix. No app bundle was modified for this test.

Evidence from a fresh session after applying the hybrid positive text to the live managed source copy:

| Run | Final size | h1 | buttons | pause |
|---|---:|---:|---:|---:|
| original `openchamber_web` | 25,777 B | 0 | 0 | 0 |
| no web + shell/Chrome | 51,702 B | 1 | 7 | 43 |
| hybrid source patch | **44,835 B** | **1** | **1** | **9** |

![success after hybrid](https://raw.githubusercontent.com/chenyuhao0628/openchamber-web-framing-evidence/main/success-after-hybrid.png)

Full evidence and root-cause note:

https://github.com/chenyuhao0628/openchamber-web-framing-evidence

Temporary workaround for users:

1. Apply the hybrid positive description to the live managed source copy of `openchamber_web`.
2. Restart the OpenCode service.
3. Start a new session.
This is not persistent across app updates/regeneration. A local persistent workaround requires patching the bundled `@openchamber/web` template and re-signing the app, which is not suitable for general users.

Why this should be fixed upstream:

- The A/B held model, prompt, and settings constant; only `openchamber_web` availability/text changed. The original framing was associated with missing page chrome/controls, while the hybrid text restored them in the tested session. That makes this a tool-affordance/correctness issue, not merely a prompt or model-capability issue. This was demonstrated on one model; cross-model behavior still needs validation.
- The deliverable is incomplete when the model treats the panel as the page UI: users get an animation but no standalone title/controls.
- Users should not need to patch app-internal copies or rerun scripts after updates.
- The tested fix is a small description/action-schema change; the executor and browser-panel behavior can remain unchanged. The tool remains useful for coding preview.
- The demonstrated scope is standalone HTML/creative tasks; other task families were not measured.

Proposed hybrid positive description:

```text
A browser viewport for visual verification of a rendered page. Use one action per
call. Open the page, inspect its rendered text, controls, and errors, and capture
screenshots at desktop/mobile sizes. The viewport is a separate verification
surface; the page provides its own rendered content and behavior.
```

Action descriptions should use the same positive framing for `browser.open`, `browser.snapshot`, `browser.capture`, `browser.resize`, and `browser.close`. No page-content requirements should be added.
