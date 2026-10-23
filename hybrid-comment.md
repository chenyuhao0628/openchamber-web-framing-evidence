Positive follow-up: the hybrid positive description works.

I changed only the `openchamber_web` description/action schema to positive wording (no `not`/`does not`; no page-content requirements):

> A browser viewport for visual verification of a rendered page. Use one action per call. Open the page, inspect its rendered text, controls, and errors, and capture screenshots at desktop/mobile sizes. The viewport is a separate verification surface; the page provides its own rendered content and behavior.

Same build prompt, model, Responses, reasoning max, max output 384,000; MCP/skills off; only `openchamber_web` enabled.

Result: **39,149 B / 998 lines; `<h1>` 1, `<button>` 1, `<p>` 101; pause 19/play 17; 11 keyframes**. The final answer includes pause/continue controls, responsive desktop/tablet/mobile layout, and reduced-motion support. The standalone page UI is back.

Comparison:

- original web: 25,777 B, no `<h1>`, no buttons
- neutral refactor: ~36,000 B, no `<h1>`, no buttons
- **hybrid positive: 39,149 B, `<h1>` + button + captions**
- no web: 51,702 B, `<h1>` + 7 buttons

The positive boundary ("the viewport is a separate verification surface; the page provides its own rendered content and behavior") appears to fix the bias without prescribing page features. This is a viable description-only fix; the neutral/negative refactor was not.

Evidence: `hybrid-positive-note.md`, `hybrid-positive-web.html`, `hybrid-positive-web.png` in https://github.com/chenyuhao0628/openchamber-web-framing-evidence
