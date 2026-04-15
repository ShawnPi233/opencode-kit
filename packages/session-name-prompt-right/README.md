# session-name-prompt-right

OpenCode plugin to show the current session name on the prompt right side.

## Language

- 中文版：[`README-zh.md`](./README-zh.md)
- English: [`README.md`](./README.md)

## What this is

- A small OpenCode TUI plugin
- It shows the current session title on the prompt right side
- It works through `tui.json` without patching OpenCode source

## One-line install

```text
Please install the OpenCode session name prompt-right plugin for me using this `INSTALL.md`.
```

## Key features

- Shows the current session title
- Falls back to a short `sessionID` until the title is loaded
- Updates automatically when the session title changes

## Version requirement

- Requires `OpenCode >= 1.4.6`
- Older versions such as `1.2.26` do not support the `plugin` field in `tui.json`, so the plugin will not load

## Files

- `INSTALL.md`: English install guide
- `INSTALL-zh.md`: Chinese install guide
