# Skills

Four small skills for any AI app that supports skills — Claude Code, the Claude app, Codex,
Cursor, Gemini, Copilot. Plain Markdown: no scripts, no installers, nothing that runs on its own.

| Skill | What changes | How to call it |
|---|---|---|
| [show-me](show-me/) | Long, many-part answers become one page you can scan instead of a wall of text. | Your AI switches to a page on its own; or say "show me". |
| [plain](plain/) | The last answer comes back as a few short, plain bullets you can read in ten seconds. | Type `/plain` after a long reply. |
| [boris](boris/) | Your AI's instruction files, memory and skills get trimmed to what's true now — archived, never deleted. | Type `/boris` when your AI starts ignoring your instructions. |
| [linus-review](linus-review/) | A cold code review: did the build do what was claimed, and what's the ranked fix plan. | Type `/linus-review` before you ship. |

## Install

Copy a skill's folder, whole, into your app's skills folder:

| App | Folder |
|---|---|
| Claude Code | `~/.claude/skills/` |
| Codex | `~/.agents/skills/` |
| Cursor | `~/.cursor/skills/` |
| Gemini CLI | `~/.gemini/skills/` |
| GitHub Copilot | `~/.copilot/skills/` |
| Claude app | zip the folder, then Customize → Skills → upload the zip |

```bash
git clone --depth 1 https://github.com/lucascashwell3-ai/skills && cp -R skills/show-me ~/.claude/skills/
```

Codex calls a skill with `$name` instead of `/name`. New skills load in a new session.

Or let [Skillproof](https://github.com/lucascashwell3-ai/Skillproof) pick and fit them for you.

## License

MIT.
