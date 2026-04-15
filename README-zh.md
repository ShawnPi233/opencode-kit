<div align="center">
  <p>
    <img src="./statics/logo.png" alt="opencode-kit logo" width="220" />
  </p>
  <h1>opencode-kit</h1>
  <p>OpenCode 安装包与配置模板集合。</p>
  <p>
    <a href="./packages/feishu-reminder/README-zh.md">Feishu Reminder</a> •
    <a href="./packages/session-name-prompt-right/README-zh.md">Session Name Prompt Right</a>
  </p>
</div>

## 语言切换

- 中文版：[`README-zh.md`](./README-zh.md)
- English: [`README.md`](./README.md)

## 这个仓库是什么

- 这是一个面向 `OpenCode` 的可分享安装包仓库
- 每个功能都放在 `packages/` 下的独立子目录
- 每个子目录都包含 `README` 和 `INSTALL` 文档，方便直接丢给 OpenCode 或人工安装

## 当前包含的安装包

- [`packages/feishu-reminder/`](./packages/feishu-reminder/README-zh.md)：飞书完成提醒
- [`packages/session-name-prompt-right/`](./packages/session-name-prompt-right/README-zh.md)：在输入框右侧显示当前 session name

## 推荐用法

- 想分享某个功能时，直接发对应子目录里的 `README-zh.md` 或 `INSTALL-zh.md`
- 想让 OpenCode 帮你安装时，直接把对应子目录的安装文档给它

## 目录结构

```text
packages/
  feishu-reminder/
  session-name-prompt-right/
```

## 快速开始

- 打开对应安装包目录，阅读它的 `README-zh.md` 或 `README.md`
- 把对应的 `INSTALL-zh.md` 或 `INSTALL.md` 直接作为 OpenCode 的安装提示词
- 新增安装包时，保持 `packages/` 下统一的 `README` + `INSTALL` 结构

## 后续扩展

- 可以继续往 `packages/` 里加更多 OpenCode 插件、skills、workflow 模板
- 只要保持统一结构，后续维护和分享都会比较顺手
