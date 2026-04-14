# opencode-reminder

OpenCode 任务完成后，自动通过飞书发送提醒的单文件安装包。

## 这个项目是什么

- 这是 `OpenCode` 飞书提醒方案的分享仓库
- 核心内容都放在 `INSTALL.md`
- 你只需要把这个 `md` 安装到自己的 `opencode` 配置目录，就能完成部署

## 一句话安装

```bash
mkdir -p ~/.config/opencode/skills/task-finish-popup && cp INSTALL.md ~/.config/opencode/skills/task-finish-popup/INSTALL.md
```

如果你已经把 `INSTALL.md` 放到别的目录，也可以把上面的 `INSTALL.md` 换成对应路径。

## 安装后做什么

- 按 `INSTALL.md` 里的步骤创建对应的 `skill`、`plugin` 和本地配置
- 启动 `opencode` 时会自动带起 `opencode web`
- 任务结束后会在飞书里收到提醒卡片

## 主要特性

- 1 分钟以上任务才提醒
- 飞书消息包含 session 标题、`sessionID`、SSH 来源、耗时和完整回复摘要
- 支持飞书卡片按钮跳转到 `opencode web`
- 不再回退到本地弹窗或终端通知

## 文件说明

- `INSTALL.md`：完整安装说明
- 适合直接分享给别人，让别人按文档完成安装
