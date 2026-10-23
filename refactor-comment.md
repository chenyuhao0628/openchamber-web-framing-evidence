Refactor test update (negative result)

I temporarily changed only the `openchamber_web` description and action schema to a neutral viewport description: removed `browser panel`, `live session`, `in-app running app`, and `leave open to show the user`; added only the tool's verification role. No page-content requirements were added.

Test setup: same build prompt, model, Responses, reasoning max, max output 384,000; MCP and skills disabled; only `openchamber_web` enabled in addition to the normal build tools; same pelican prompt.

Result: the neutral refactored description still produced an embedded animation-only page: ~36 KB, no `<h1>`, no buttons/text. For comparison, the no-web arm produced 51,702 B / 1,118 lines with `<h1>` + postcard + 7 buttons / 43 pause; the original web arm produced 25,777 B / 542 lines with no `<h1>`/buttons.

This suggests the bias is the tool's presence/affordance (the browser panel remains the model's presentation surface), not merely its wording. A description-only change is insufficient. The effective workaround is to disable `openchamber_web` for standalone creative tasks, or change the tool/agent workflow rather than only the text.

Evidence: `refactor-note.md`, `refactor-neutral-web.html`, `refactor-neutral-web.png` in https://github.com/chenyuhao0628/openchamber-web-framing-evidence
