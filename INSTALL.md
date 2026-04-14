# OpenCode Feishu Reminder Install Guide

This is the English install guide for the OpenCode Feishu reminder workflow.

## What it does

- Sends a reminder only for tasks longer than 1 minute
- Skips reminders when you are already viewing the same session
- Feishu messages include:
  - session title
  - `sessionID`
  - SSH source
  - duration
  - full reply summary
- Automatically starts `opencode web`

## Why this approach

- No extra permissions beyond your existing Feishu bot webhook
- Lightweight flow saves tokens compared with heavier helper workflows
- Easy to share: one install guide is enough

## Where to install

Place the files in your OpenCode config directory:

- `~/.config/opencode/skills/task-finish-popup/SKILL.md`
- `~/.config/opencode/plugins/task-finish-popup.ts`
- `~/.config/opencode/plugins/task-finish-popup.local.json`

## Prerequisites

- A Feishu custom bot webhook
- If you use SSH from a Mac to a remote Linux host, enable port forwarding:

```bash
ssh -L 4096:127.0.0.1:4096 <user>@<host>
```

Then confirm this URL opens locally:

```text
http://127.0.0.1:4096
```

## Install steps

1. Follow `INSTALL.md` (Chinese) or copy the same code blocks into the files listed above.
2. Put your Feishu webhook in `task-finish-popup.local.json` or export `OPENCODE_FEISHU_WEBHOOK`.
3. Use the `opencode` wrapper script so Web starts automatically and a free port is selected if needed.
4. Run `opencode`, then test with a task longer than 1 minute.

## Quick install prompt

```text
Please install OpenCode Feishu completion reminders for me using this `INSTALL_EN.md`.
```

## Notes

- The Chinese version is `INSTALL.md`
- The full implementation details and code blocks live in the Chinese guide
- This file is for English-language installation instructions and sharing
