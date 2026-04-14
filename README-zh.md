# opencode-reminder

OpenCode 飞书完成提醒安装包。

## 语言切换

- 中文版：[`README-zh.md`](./README-zh.md)
- English: [`README.md`](./README.md)

## 这个项目是什么

- 这是 `OpenCode` 飞书提醒方案的分享仓库
- 安装说明分别放在 `INSTALL.md`（English）和 `INSTALL-zh.md`（中文）
- 按对应语言的安装说明配置，就能完成部署

## 一句话安装

```text
请帮我根据这个 `INSTALL-zh.md` 安装 OpenCode 飞书完成提醒。
```

## 安装后做什么

- 按安装说明创建对应的 `skill`、`plugin` 和本地配置
- 启动 `opencode` 时会自动带起 `opencode web`
- 任务结束后会在飞书里收到提醒卡片

## 主要特性

- 1 分钟以上任务才提醒
- 飞书消息包含 session 标题、`sessionID`、SSH 来源、耗时和完整回复摘要
- 支持飞书卡片按钮跳转到 `opencode web`
- 不再回退到本地弹窗或终端通知

## 文件说明

- `INSTALL-zh.md`：中文完整安装说明
- `INSTALL.md`：English install guide
