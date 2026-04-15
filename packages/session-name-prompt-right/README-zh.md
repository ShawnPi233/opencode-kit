# session-name-prompt-right

OpenCode 输入框右侧 session name 显示插件。

## 语言切换

- 中文版：[`README-zh.md`](./README-zh.md)
- English: [`README.md`](./README.md)

## 这个项目是什么

- 这是一个 OpenCode TUI 小插件
- 它会在输入框右侧显示当前 session 标题
- 不修改 OpenCode 源码，只通过 `tui.json` 注册插件

## 一句话安装

```text
请帮我根据这个 `INSTALL-zh.md` 安装 OpenCode 的 session name prompt right 插件。
```

## 主要特性

- 显示当前 session title
- title 还没取到时显示短 `sessionID`
- session 重命名后自动刷新

## 版本要求

- 需要 `OpenCode >= 1.4.6`
- 较老版本（例如 `1.2.26`）还不支持 `tui.json` 里的 `plugin` 字段，会导致插件无法加载

## 文件说明

- `INSTALL-zh.md`：中文完整安装说明
- `INSTALL.md`：English install guide
