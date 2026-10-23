# openchamber_web framing biases standalone HTML tasks toward embedded previews

## Summary

In OpenChamber 2.5.1, the `openchamber_web` tool description and action schema frame a page as **"OpenChamber's browser panel"**, a place to **"check your work"**, and a **"live session"**. In standalone HTML generation tasks, this framing appears to make the model treat the browser panel as the page's UI: it omits the page's own title, text/layout, and controls, and produces a much smaller embedded-style preview.

The effect is reproducible with the same model, prompt, and settings; the only variable is whether `openchamber_web` is available. Disabling it produces a full standalone page with a title, postcard-style text, and pause/continue controls, at roughly twice the size.

This is a tool-description/affordance issue, not a model capability issue and not a problem with the user's `build.md`.

## Environment

- OpenChamber: 2.5.1 (desktop app)
- OpenCode: 2.1.3 (bundled runtime)
- OS: macOS 26.5.2, arm64
- Provider/model: `opencode-go/deepseek-v4.1-flash#max`
- Protocol: OpenAI-compatible Responses
- Reasoning: max
- Max output tokens: 384,000
- MCP: disabled (all configured servers disabled)
- Skills: disabled (`skills: []`)

The tests used the current build-agent prompt (5,606 characters) for the "build" arms and a 46-character system prompt with no write/edit tools for the "minimal" arms. The same prompt was used for every arm.

## Actual `openchamber_web` description

> OpenChamber's browser panel, to check your work by looking at it. One action per call: open, snapshot for text, selectors and errors, then click, type or select or scroll by those selectors. Close tabs you opened for your own work with browser.close; leave open the ones you opened to show the user something. The page runs with the user's real logins; treat it as their live session.

Relevant action descriptions in the same schema:

- `browser.open`: "Open url in the in-app browser panel; use it to look at the running app."
- `browser.capture`: "Save what is currently visible in the browser panel as an image file in the project and return its path, so a change can be shown rather than described."
- `browser.resize`: "fill uses the whole panel."

The description is 374 characters; the tool schema is about 2.8 KB. The issue is the framing, not the size.

## A/B measurements

| Configuration | Final size | Lines | h1 / postcard | buttons / pause | keyframes | Web calls | Write/edit path |
|---|---:|---:|---:|---:|---:|---:|---|
| build prompt + `openchamber_web` | 25,777 B | 542 | 0 / 0 | 0 / 0 | 6 | 29 | 1 write (23,384 B) + 21 edits |
| build prompt + no `openchamber_web` | **51,702 B** | **1,118** | **1 / 1** | **7 / 43** | **23** | **0** | 1 write + 17 edits; 21 shell / 15 read |
| minimal 46-char + `openchamber_web` | 28,155 B | 656 | 0 / 0 | 0 / 0 | 4 | 11 | shell; no write/edit |
| minimal 46-char + no `openchamber_web` | 44,033 B | 1,021 | 1 / 4 | 0 / 0 | 13 | 0 | shell; no write/edit |
| DSH reference (other app) | 32,661 B | 767 | 1 / 0 | 6 / 23 | 7 | 0 | bash + Chrome/read_image/CUA |
| OpenCode shell/Chrome reference | 34,329 B | 672 | 1 / 0 | 14 / 18 | 1 | 0 | 1 write (33,769 B) + 5 edits |

Notes:

- The build + web vs build + no-web runs used the same model, model variant, Responses protocol, max output, prompt, MCP-off, skills-off, and auto-approval. Only `openchamber_web` availability changed.
- The minimal arms used a 46-character system prompt and denied `edit` (so there were no write/edit/patch tools); the only change was `openchamber_web`.
- The DSH and OpenCode shell/Chrome rows are references, not controlled OpenChamber A/B arms. They show the same direction: shell/Chrome visual verification produces a standalone page.
- The OpenCode shell/Chrome reference was not a clean MCP/skills-controlled run; it is included only because it shows the model can produce page controls in that path.

## Observations

1. **With `openchamber_web`**, the model writes an animation-only file. Both the build and minimal web runs have **0 `<h1>`, 0 buttons**, and 25.8-28.1 KB. The final answer describes the animation; the browser panel is treated as the display surface.
2. **Without `openchamber_web`**, the model writes a standalone page. The build no-web run has **`<h1>`, postcard layout, 133 `<p>` elements, 7 buttons/43 pause**, and 51.7 KB. The minimal no-web run has a title/postcard but no buttons (44.0 KB); the buttons are a separate model/task choice.
3. **Prompt/write-edit/tool count are not the cause.** The build prompt's "smallest change" and write/edit tools were retained in the no-web run, yet it produced 51.7 KB and controls. The minimal no-web run had no write/edit and also produced a standalone 44.0 KB page. The 88-tool DSH reference is not required: the OpenChamber no-web runs used far fewer tools.
4. **Output caps are not binding.** All outputs are far below 256k/384k. The A/B difference is not a truncation effect.
5. **The tool description/schema frames the page as an app preview.** The phrases "browser panel", "check your work", "look at the running app", "live session", "leave open ... to show the user", and "fill uses the whole panel" all position the panel as the page's UI, so the model omits its own page chrome and controls.

## Reproduction

1. Use OpenChamber 2.5.1 with OpenCode 2.1.3, model `opencode-go/deepseek-v4.1-flash#max`, Responses, reasoning max, max output 384,000.
2. Disable all MCP servers and skills. Use the current build-agent prompt.
3. Run arm A with `openchamber_web` available.
4. Run arm B with the same agent and settings, but deny `openchamber_web` (for example, a broad deny followed by allow rules for `shell`, `read`, `glob`, `grep`, `edit`, `webfetch`, `websearch`). Auto-approve permissions.
5. Send the same prompt:

```text
创建一个 HTML，内容是 SVG 绘制一个鹈鹕骑自行车的 2D 动画，你不需要任何测试
```

6. Compare the output. Arm A produced 25,777 B / 542 lines with no `<h1>` and no buttons. Arm B produced 51,702 B / 1,118 lines with a title/postcard and 7 buttons/43 pause. The only changed variable was `openchamber_web`.

The minimal A/B repeats the same comparison with a 46-character system prompt and no write/edit tools: 28,155 B vs 44,033 B.

## Expected behavior

When `openchamber_web` is used only for visual verification, the agent should still produce a **standalone deliverable**: the page should include its own title, text/layout, and controls. The browser panel should be a viewport, not the page's UI.

## Suggested fix

Option 1 (smallest): re-frame the tool description/action schema, for example:

> Use this browser viewport only to verify a standalone page. The page must remain a standalone deliverable and include its own title, content, and controls. Do not treat the browser panel as the page's UI.

Option 2: provide a built-in creative/standalone agent profile that excludes `openchamber_web`, and document that profile for standalone HTML/creative work. The normal coding preview path can keep the current tool framing.

Option 3 (documentation only): document the permission workaround (deny `openchamber_web` for creative agents) and the difference between the panel-preview and standalone-page workflows.

## Evidence files

- `with-web.html` / `with-web.png`: build prompt + `openchamber_web` (25,777 B; no page title/buttons).
- `without-web.html` / `without-web.png`: build prompt + no `openchamber_web` (51,702 B; title, postcard text, pause/continue).
- `minimal-with-web.html` / `.png` and `minimal-without-web.html` / `.png`: 46-character/no-write-edit A/B.
- `dsh-reference.html` / `.png`: non-OpenChamber reference with shell/Chrome/CUA (32,661 B; controls).
- `opencode-shell-chrome-reference.html` / `.png`: OpenCode shell/Chrome reference (34,329 B; controls; not clean MCP/skills-controlled).
- `measurements.csv`: the numeric table.
- `tool-description.md`: the exact description/action quotes.
- `repro.md`: sanitized reproduction config and prompt.

All screenshots are page-only (1440x900, headless Chrome); they do not contain the OpenChamber UI, browser tabs, local paths, session IDs, or user data. The HTML files were scanned for local paths, usernames, tokens, and session IDs; no matches were found.

## Caveats

- The A/B was run with one model/provider. The magnitude may vary by model; the reported variable is the tool framing.
- The button count is a model/task choice: the minimal no-web arm produced a standalone page but no buttons, while the build no-web and shell/Chrome reference arms did. The consistent part is that the `openchamber_web` arms never produced standalone page chrome/controls.
- The DSH reference is a different app/agent composition and is included only as a directional reference.
