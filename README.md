# opencode-reminder

OpenCode 飞书完成提醒的单文件安装包。 / A single-file install package for OpenCode Feishu completion reminders.

## Language / 语言

- [中文](#中文)
- [English](#english)

## 中文

### 这个项目是什么

- 这是 `OpenCode` 飞书提醒方案的分享仓库
- 核心内容都放在 `INSTALL.md`（中文）和 `INSTALL_EN.md`（English）
- 你只需要把对应语言的 `md` 按文档安装到自己的 `opencode` 配置目录，就能完成部署

### 一句话安装

```text
请帮我根据这个 `INSTALL.md` 安装 OpenCode 飞书完成提醒。
```

英文版提示词：

```text
Please install OpenCode Feishu completion reminders for me using this `INSTALL_EN.md`.
```

### 安装后做什么

- 按 `INSTALL.md` 里的步骤创建对应的 `skill`、`plugin` 和本地配置
- 启动 `opencode` 时会自动带起 `opencode web`
- 任务结束后会在飞书里收到提醒卡片

### 主要特性

- 1 分钟以上任务才提醒
- 飞书消息包含 session 标题、`sessionID`、SSH 来源、耗时和完整回复摘要
- 支持飞书卡片按钮跳转到 `opencode web`
- 不再回退到本地弹窗或终端通知

### 文件说明

- `INSTALL.md`：中文完整安装说明
- `INSTALL_EN.md`：English install guide
- 适合直接分享给别人，让别人按文档完成安装

## English

### What is this

- A shared repository for the `OpenCode` Feishu reminder workflow
- The full setup lives in `INSTALL.md`
- Install that `md` into your `opencode` config following the guide, and you're done

### One-line install

```text
Please install OpenCode Feishu completion reminders for me using this `INSTALL.md`.
```

### What happens after install

- Create the required `skill`, `plugin`, and local config as described in `INSTALL.md`
- `opencode web` will be started automatically when you launch `opencode`
- You will receive a Feishu card when a task finishes

### Key features

- Only tasks longer than 1 minute trigger reminders
- Feishu messages include the session title, `sessionID`, SSH source, duration, and full reply summary
- Supports a button that jumps to `opencode web`
- No local popup or terminal fallback

### Files

- `INSTALL.md`: full installation guide
- Easy to share with others for self-installation
