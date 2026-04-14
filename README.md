# opencode-reminder

OpenCode Feishu completion reminder package.

## Language

- 中文版：[`README-zh.md`](./README-zh.md)
- English: [`README.md`](./README.md)

## What this is

- A shared repository for the OpenCode Feishu reminder workflow
- The setup guide lives in `INSTALL.md`
- Use the install guide to install the reminder into your `opencode` config

## One-line install

```text
Please install OpenCode Feishu completion reminders for me using this `INSTALL.md`.
```

## After install

- `opencode` will auto-start `opencode web`
- Long-running tasks will trigger a Feishu reminder card

## Key features

- 1-minute threshold
- Feishu message includes session title, `sessionID`, SSH source, duration, and full reply summary
- Feishu card button opens `opencode web`

## Why this approach

- No extra permissions needed beyond your existing Feishu bot webhook
- Keeps the flow lightweight and saves tokens compared with heavier helper workflows
- Easy to share: one install guide, one reminder workflow

## Files

- `INSTALL.md`: English install guide
- `INSTALL-zh.md`: Chinese install guide
