# Refactor test: neutral `openchamber_web` description

## What changed

Temporarily changed only the `openchamber_web` description and action schema to a neutral viewport description:

```text
A browser viewport for visually verifying a rendered page. The viewport is a
verification surface, not the page UI. Use one action per call. Open the page,
inspect its rendered text, controls, and errors, and capture screenshots at
desktop/mobile sizes. The viewport does not add page content or behavior.
```

No page content requirements were added. The old framing (`browser panel`, `live session`, `in-app running app`, `leave open to show the user`) was removed.

## Test setup

- Same build prompt, model, Responses, reasoning max, max output 384,000.
- MCP and skills disabled.
- Only `openchamber_web` enabled in addition to the normal build tools.
- Same pelican prompt.

## Result

- With the neutral refactored description: **~36 KB, no `<h1>`, no buttons/text** (embedded animation-only page).
- Without `openchamber_web`: 51,702 B / 1,118 lines, `<h1>` + postcard, 7 buttons / 43 pause.
- Original `openchamber_web`: 25,777 B / 542 lines, no `<h1>`, no buttons.

The neutral description increased size/detail but did not restore standalone page UI. This suggests the bias is the tool's presence/affordance (the browser panel remains the model's presentation surface), not just the wording.

## Conclusion

A description-only refactor is insufficient. The effective workaround is to disable `openchamber_web` for standalone creative tasks, or to change the tool/agent workflow rather than only the text.

Files: `refactor-neutral-web.html`, `refactor-neutral-web.png`.
