# OpenCode Session Name Prompt Right Install Guide

This is a single-file install guide.

It installs a small TUI plugin that shows the current session name on the prompt right side.

## What it does

- Shows the current session title on the prompt right side
- Falls back to a short `sessionID` until the title is loaded
- Updates automatically when the session title changes
- Avoids patching OpenCode source

## Prerequisites

- Your OpenCode version supports TUI plugins and the `session_prompt_right` slot
- Your OpenCode config directory is writable

Run:

```bash
opencode debug paths
```

Use the `config` path from the output as `<OPENCODE_CONFIG_DIR>` below.

## Install steps

1. Create `<OPENCODE_CONFIG_DIR>/plugins/session-name-prompt-right.tsx` using the code from `INSTALL-zh.md`.
2. Add `./plugins/session-name-prompt-right.tsx` to `<OPENCODE_CONFIG_DIR>/tui.json`.
3. Restart `opencode`.

## Quick install prompt

```text
Please install the OpenCode session name prompt-right plugin for me using this `INSTALL.md`.
```
