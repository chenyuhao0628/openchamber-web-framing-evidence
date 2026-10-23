# Hybrid positive prompt test: success

## Prompt

```text
A browser viewport for visual verification of a rendered page. Use one action per call. Open the page, inspect its rendered text, controls, and errors, and capture screenshots at desktop/mobile sizes. The viewport is a separate verification surface; the page provides its own rendered content and behavior.
```

Action descriptions use the same positive framing (no `not`, `does not`, or page-content requirements).

## Setup

- Same build prompt, model, Responses, reasoning max, max output 384,000.
- MCP and skills disabled.
- Only `openchamber_web` enabled in addition to the normal build tools.
- Same pelican prompt.

## Result

- **39,149 B / 998 lines**
- `<h1>` 1, `<button>` 1, `<p>` 101
- pause 19, play 17, keyframes 11
- Final answer includes pause/continue controls, responsive desktop/tablet/mobile layout, and reduced-motion support.

Comparison:

| Run | Final | h1 | buttons | text |
|---|---:|---:|---:|---:|
| original web | 25,777 B | 0 | 0 | no page UI |
| neutral refactor | ~36,000 B | 0 | 0 | no page UI |
| **hybrid positive** | **39,149 B** | **1** | **1** | title + captions + controls |
| no web | 51,702 B | 1 | 7 | full standalone page |

## Conclusion

The hybrid positive description restored standalone page UI while keeping `openchamber_web` enabled. The key difference from the neutral refactor is the positive boundary: "The viewport is a separate verification surface; the page provides its own rendered content and behavior." It does not prescribe page features. This wording is a viable description-only fix.

Files: `hybrid-positive-web.html`, `hybrid-positive-web.png`.
