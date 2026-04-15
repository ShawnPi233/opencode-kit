<div align="center">
  <p>
    <img src="./statics/logo.png" alt="opencode-kit logo" width="220" />
  </p>
  <h1>opencode-kit</h1>
  <p>A collection of shareable OpenCode install packs and config templates.</p>
  <p>
    <a href="./packages/feishu-reminder/README.md">Feishu Reminder</a> •
    <a href="./packages/session-name-prompt-right/README.md">Session Name Prompt Right</a>
  </p>
</div>

## Language

- 中文版：[`README-zh.md`](./README-zh.md)
- English: [`README.md`](./README.md)

## What this repository is

- A shared repository of reusable OpenCode packages
- Each feature lives in its own folder under `packages/`
- Each package includes `README` and `INSTALL` docs for easy sharing and agent-assisted setup

## Included packages

- [`packages/feishu-reminder/`](./packages/feishu-reminder/README.md): Feishu completion reminders
- [`packages/session-name-prompt-right/`](./packages/session-name-prompt-right/README.md): show the current session name on the prompt right side

## Recommended usage

- Share a package by sending its `README-zh.md` or `INSTALL-zh.md`
- Ask OpenCode to install a feature using the package-specific install guide

## Layout

```text
packages/
  feishu-reminder/
  session-name-prompt-right/
```

## Quick Start

- Open a package folder and read its `README.md` or `README-zh.md`
- Use the package-specific `INSTALL.md` or `INSTALL-zh.md` as the installation prompt for OpenCode
- Keep new packages under `packages/` with the same `README` + `INSTALL` structure

## Extending the repo

- Add more OpenCode plugins, skills, or workflow templates under `packages/`
- Keeping the same layout makes the repo easier to maintain and share
