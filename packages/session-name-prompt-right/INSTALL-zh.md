# OpenCode Session Name Prompt Right 安装说明

这是一份单文件安装文档。

按文档创建一个 TUI 插件，并在 `tui.json` 中注册它，即可在 OpenCode 输入框右侧显示当前 session name。

## 功能

- 在 OpenCode TUI 输入框右侧显示当前 session 标题
- session 标题生成前，先显示短 `sessionID`，例如 `ses_2751`
- session 标题更新时自动刷新显示
- 不修改 OpenCode 源码，升级 OpenCode 时更容易保留

## 适用场景

- 希望在当前窗口直接看到正在操作的 session name
- 不想维护 OpenCode 源码 patch
- 接受显示位置在输入框右侧，而不是 context usage 同一行

## 前置条件

- 需要 `OpenCode >= 1.4.6`，该版本开始可在 `tui.json` 中使用 `plugin` 字段加载 TUI 插件
- 较老版本（例如 `1.2.26`）即使照文档配置，也会因为不识别 `tui.json` 里的 `plugin` 字段而加载失败
- OpenCode 配置目录可写

先执行：

```bash
opencode debug paths
```

找到输出里的 `config` 路径。下面文档里的 `<OPENCODE_CONFIG_DIR>` 都指这个目录。

## 第一步：创建插件文件

把下面内容保存到：

```text
<OPENCODE_CONFIG_DIR>/plugins/session-name-prompt-right.tsx
```

```tsx
/** @jsxImportSource @opentui/solid */
import { createEffect, createSignal, onCleanup } from "solid-js"
import type { TuiPlugin, TuiPluginModule, TuiPluginApi } from "@opencode-ai/plugin/tui"

const MAX_TITLE_LENGTH = 32

function truncate(value: string, length: number) {
  return value.length > length ? `${value.slice(0, length - 3)}...` : value
}

function cleanTitle(title: unknown) {
  if (typeof title !== "string") return undefined
  const normalized = title.replace(/\s+/g, " ").trim()
  return normalized ? truncate(normalized, MAX_TITLE_LENGTH) : undefined
}

function SessionName(props: { api: TuiPluginApi; sessionID: string }) {
  const [title, setTitle] = createSignal<string | undefined>()

  createEffect(() => {
    let disposed = false
    const sessionID = props.sessionID

    props.api.client.session
      .get({ sessionID })
      .then((result) => {
        if (!disposed) setTitle(cleanTitle(result.data?.title))
      })
      .catch(() => {})

    const offUpdated = props.api.event.on("session.updated", (event) => {
      if (event.properties.sessionID === sessionID) {
        setTitle(cleanTitle(event.properties.info.title))
      }
    })

    const offCreated = props.api.event.on("session.created", (event) => {
      if (event.properties.sessionID === sessionID) {
        setTitle(cleanTitle(event.properties.info.title))
      }
    })

    onCleanup(() => {
      disposed = true
      offUpdated()
      offCreated()
    })
  })

  return (
    <text fg={props.api.theme.current.textMuted} wrapMode="none">
      {title() ?? props.sessionID.slice(0, 8)}
    </text>
  )
}

const tui: TuiPlugin = async (api) => {
  api.slots.register({
    order: 100,
    slots: {
      session_prompt_right(_ctx, props) {
        return <SessionName api={api} sessionID={props.session_id} />
      },
    },
  })
}

const plugin: TuiPluginModule & { id: string } = {
  id: "session-name-prompt-right",
  tui,
}

export default plugin
```

## 第二步：注册 TUI 插件

打开：

```text
<OPENCODE_CONFIG_DIR>/tui.json
```

如果文件不存在，创建下面内容：

```json
{
  "$schema": "https://opencode.ai/tui.json",
  "plugin": [
    "./plugins/session-name-prompt-right.tsx"
  ]
}
```

如果文件已经存在，只需要把插件路径加入 `plugin` 数组。

## 第三步：重启 OpenCode

```bash
opencode
```

进入任意 session 后，输入框右侧应显示当前 session 标题。

## 注意事项

- 这个方案只使用 TUI 插件，不修改 OpenCode 源码
- 显示位置由 `session_prompt_right` slot 决定，在输入框右侧
- 如果想把 session name 放到 context usage 同一行，需要修改 OpenCode 源码
- 窗口过窄时，右侧标题可能因为布局空间不足而换行或重排
