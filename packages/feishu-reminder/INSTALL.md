# OpenCode Feishu Reminder Install Guide

This is the English install guide for the OpenCode Feishu reminder workflow.

## What it does

- Sends a reminder only for tasks longer than 1 minute
- Skips reminders when you are already viewing the same session
- Feishu messages include session title, `sessionID`, SSH source, duration, and full reply summary
- Automatically starts `opencode web`

## Where to install

Place the files in your OpenCode config directory:

- `~/.config/opencode/skills/task-finish-popup/SKILL.md`
- `~/.config/opencode/plugins/task-finish-popup.ts`
- `~/.config/opencode/plugins/task-finish-popup.local.json`

## Install steps

1. Follow `INSTALL-zh.md` for the full code blocks.
2. Put your Feishu webhook in `task-finish-popup.local.json` or export `OPENCODE_FEISHU_WEBHOOK`.
3. Run `opencode`, then test with a task longer than 1 minute.

## Quick install prompt

```text
Please install OpenCode Feishu completion reminders for me using this `INSTALL.md`.
```
