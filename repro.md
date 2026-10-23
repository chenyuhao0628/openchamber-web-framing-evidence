# Sanitized reproduction

## Environment

- OpenChamber 2.5.1
- OpenCode 2.1.3 (bundled runtime)
- macOS 26.5.2 arm64
- Provider/model: `opencode-go/deepseek-v4.1-flash#max`
- Protocol: OpenAI-compatible Responses; reasoning max; max output 384,000
- MCP: all servers disabled
- Skills: disabled (`skills: []`)

## Prompt

```text
创建一个 HTML，内容是 SVG 绘制一个鹈鹕骑自行车的 2D 动画，你不需要任何测试
```

## Arm A: build prompt + openchamber_web

- Use the current build-agent prompt (5,606 characters).
- Keep `write`, `edit`, `shell`, `read`, etc.
- Keep `openchamber_web` available.
- Auto-approve permissions.

Observed: 25,777 B / 542 lines; no `<h1>`, no buttons; 29 `openchamber_web` calls; 1 write (23,384 B) + 21 edits.

## Arm B: same settings, openchamber_web denied

Same agent, model, prompt, MCP-off, skills-off, and auto-approval. Deny `openchamber_web` and allow the rest of the build tools. For example:

```jsonc
"permissions": [
  { "action": "*", "resource": "*", "effect": "deny" },
  { "action": "shell", "resource": "*", "effect": "allow" },
  { "action": "read", "resource": "*", "effect": "allow" },
  { "action": "glob", "resource": "*", "effect": "allow" },
  { "action": "grep", "resource": "*", "effect": "allow" },
  { "action": "edit", "resource": "*", "effect": "allow" },
  { "action": "webfetch", "resource": "*", "effect": "allow" },
  { "action": "websearch", "resource": "*", "effect": "allow" }
]
```

Observed: 51,702 B / 1,118 lines; `<h1>` + postcard layout; 7 buttons / 43 pause; 23 keyframes; 0 `openchamber_web` calls; 1 write + 17 edits; 21 shell + 15 read.

## Minimal control A/B

Repeat with a 46-character system prompt:

```text
You are a helpful software engineer assistant.
```

Deny `edit` (so write/edit/patch are unavailable). Run once with `openchamber_web` allowed and once denied.

- With web: 28,155 B / 656 lines; 0 `<h1>`, 0 buttons; 11 web calls.
- Without web: 44,033 B / 1,021 lines; `<h1>` + postcard; 0 buttons; 13 keyframes; 23 shell / 14 read.

## Files

- `with-web.html` / `with-web.png`
- `without-web.html` / `without-web.png`
- `minimal-with-web.html` / `minimal-with-web.png`
- `minimal-without-web.html` / `minimal-without-web.png`
- `dsh-reference.html` / `dsh-reference.png`
- `opencode-shell-chrome-reference.html` / `opencode-shell-chrome-reference.png`

Screenshots are page-only at 1440x900. The HTML files were scanned for local paths, usernames, tokens, and session IDs; no matches were found.
