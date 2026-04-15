# OpenCode 飞书完成提醒单文件安装说明

这是一份单文件安装文档。

你只需要这一个 `INSTALL.md`，按文档里的内容创建对应文件，即可完成整套功能安装。

## 功能

- 任务持续超过 1 分钟才提醒
- 当前正在查看的 session 不提醒，只提醒未读 session
- 飞书消息包含：
  - AI 生成的 session 标题
  - `sessionID`
  - 耗时
- assistant 最后一段完整回复摘要
  - 可点击的 Session 跳转按钮
- 默认自动确保 `opencode web` 已启动，优先复用已有 Web，否则自动选择 `4096-4100` 中的空闲端口

## 相比龙虾的优势

- 不需要额外权限，只要现有飞书机器人 webhook
- 流程更轻，少一层依赖，节省 token
- 分享更简单：一个安装说明就够了

## 适用场景

- 本机直接运行 `opencode`
- Mac 通过 SSH 使用远端 Linux 上的 `opencode`

## 前置条件

### 1. 准备飞书机器人 webhook

在飞书群中添加自定义机器人，拿到 webhook 地址。

### 2. 准备 OpenCode Web 可访问地址

如果你是 Mac SSH 到远端 Linux，建议使用 SSH 端口转发：

```bash
ssh -L 4096:127.0.0.1:4096 <user>@<host>
```

之后本机浏览器确认这个地址可打开：

```text
http://127.0.0.1:4096
```

## 第一步：创建 Skill 文件

把下面内容保存到：

```text
~/.config/opencode/skills/task-finish-popup/SKILL.md
```

```md
---
name: task-finish-popup
description: 为 OpenCode 任务结束时弹出桌面提醒；主要通过同目录插件自动生效。
compatibility: opencode
---

## What I do
- 为 OpenCode 持续超过 1 分钟的任务在结束时发送提醒
- 优先通过飞书机器人 webhook 发送消息
- 提醒内容包含真实 session 标题、`sessionID`、SSH 来源、耗时和完整回复摘要
- 当完成的是你当前正在查看的 session 时，不发送飞书未读提醒
- 可配合本地 `opencode` 包装脚本，默认启动 `opencode web` 供飞书链接跳转
- 优先尝试把弹窗附着到当前活跃窗口
- 仅通过飞书机器人发送提醒，不再回退到本地弹窗或终端消息

## When to use me
当你希望 OpenCode 只在较长任务完成后主动提醒你时使用。
如果你要改提醒文案、超时时间或通知方式，也可以用我作为维护入口。

## Rules
- 自动提醒依赖同目录的插件文件，而不是单靠 skill 本身
- 飞书 webhook 仅从环境变量 `OPENCODE_FEISHU_WEBHOOK` 读取，不再落盘到本地配置文件
- Session 链接只在可访问地址可确定时才显示；优先使用 `OPENCODE_PUBLIC_URL` 或 `publicBaseUrl`
- 若使用包装脚本作为默认入口，执行 `opencode` 时会自动确保 `http://127.0.0.1:4096` 的 Web 服务已启动
- 未读判断基于 `tui.session.select` 事件追踪当前活跃 session
- macOS 优先使用 `terminal-notifier`，其次回退到 `osascript`
- Linux 下优先尝试 `zenity + xdotool` 附着活跃窗口
- 若当前环境没有图形通知能力，允许降级到 `notify-send` 或终端铃声
- 仅当任务持续时间不少于 1 分钟，且会话进入 `session.idle` 时提醒

## Output style
- 先说明提醒是否已启用
- 如有降级行为，明确说明当前使用的通知方式
- 如缺少依赖，只指出最关键的安装项
- 如飞书未生效，优先检查 `OPENCODE_FEISHU_WEBHOOK` 是否已在当前 shell 环境导出
```

## 第二步：创建插件文件

把下面内容保存到：

```text
~/.config/opencode/plugins/task-finish-popup.ts
```

```ts
import { readFileSync } from "node:fs";
import os from "node:os";
import { dirname, join } from "node:path";
import { fileURLToPath } from "node:url";

const MIN_NOTIFY_DURATION_MS = 60_000;
const SESSION_CONTEXT_RETRY_MS = 1200;
const SESSION_CONTEXT_RETRY_COUNT = 3;
const sessionStartTimes = new Map();
let activeSessionID;
const localConfigPath = join(dirname(fileURLToPath(import.meta.url)), "task-finish-popup.local.json");

function getLocalConfig() {
  try {
    return JSON.parse(readFileSync(localConfigPath, "utf8"));
  } catch {
    return {};
  }
}

function getFeishuWebhook() {
  return process.env.OPENCODE_FEISHU_WEBHOOK || getLocalConfig().feishuWebhook;
}

function getPublicBaseUrl(serverUrl) {
  return process.env.OPENCODE_PUBLIC_URL || getLocalConfig().publicBaseUrl || serverUrl?.origin;
}

function hasExplicitPublicBaseUrl() {
  return Boolean(process.env.OPENCODE_PUBLIC_URL || getLocalConfig().publicBaseUrl);
}

function isLikelyReachableBaseUrl(baseUrl) {
  if (!baseUrl) {
    return false;
  }

  if (hasExplicitPublicBaseUrl()) {
    return true;
  }

  try {
    const url = new URL(baseUrl);
    return !["127.0.0.1", "localhost", "0.0.0.0"].includes(url.hostname);
  } catch {
    return false;
  }
}

function base64UrlEncode(value) {
  return Buffer.from(value, "utf8").toString("base64").replace(/\+/g, "-").replace(/\//g, "_").replace(/=/g, "");
}

function buildSessionUrl({ directory, serverUrl, sessionID }) {
  const baseUrl = getPublicBaseUrl(serverUrl);
  if (!isLikelyReachableBaseUrl(baseUrl) || !directory || !sessionID) {
    return undefined;
  }

  return new URL(`/${base64UrlEncode(directory)}/session/${sessionID}`, baseUrl).href;
}

function getSSHSourceLabel() {
  const sshConnection = process.env.SSH_CONNECTION?.trim();
  const clientIP = sshConnection?.split(/\s+/)[0];
  const hostname = os.hostname();

  if (clientIP && hostname) {
    return `${clientIP} @ ${hostname}`;
  }

  return clientIP || hostname || undefined;
}

function clampText(text) {
  if (!text) {
    return undefined;
  }

  const normalizedLines = text
    .split("\n")
    .map((line) => line.replace(/\s+/g, " ").trim())
    .filter(Boolean);

  const normalized = normalizedLines.join("\n");
  if (!normalized) {
    return undefined;
  }

  return normalized;
}

async function getSessionContext(serverUrl, directory, sessionID) {
  try {
    const [sessionResponse, messagesResponse] = await Promise.all([
      fetch(new URL(`/session/${sessionID}?directory=${encodeURIComponent(directory)}`, serverUrl)),
      fetch(new URL(`/session/${sessionID}/message?directory=${encodeURIComponent(directory)}&limit=20`, serverUrl)),
    ]);

    if (!sessionResponse.ok || !messagesResponse.ok) {
      throw new Error("failed to load session context");
    }

    const [session, messages] = await Promise.all([sessionResponse.json(), messagesResponse.json()]);
    const assistantText = [...messages]
      .reverse()
      .filter((message) => message.info.role === "assistant")
      .map((message) =>
        clampText(
          message.parts
            ?.filter((part) => part.type === "text" && !part.synthetic && !part.ignored)
            .map((part) => part.text)
            .join("\n"),
        ),
      )
      .find(Boolean);

    return {
      title: session.title,
      assistantText,
    };
  } catch {
    return {
      title: undefined,
      assistantText: undefined,
    };
  }
}

function sleep(ms) {
  return new Promise((resolve) => setTimeout(resolve, ms));
}

async function getSessionContextWithRetry(serverUrl, directory, sessionID) {
  let lastContext = {
    title: undefined,
    assistantText: undefined,
  };

  for (let attempt = 0; attempt < SESSION_CONTEXT_RETRY_COUNT; attempt += 1) {
    lastContext = await getSessionContext(serverUrl, directory, sessionID);
    if (lastContext.title || lastContext.assistantText) {
      return lastContext;
    }
    await sleep(SESSION_CONTEXT_RETRY_MS);
  }

  return lastContext;
}

async function notifyWithFeishu(message, options = {}) {
  const webhook = getFeishuWebhook();
  if (!webhook) {
    return false;
  }

  const body = options.sessionUrl
    ? {
        msg_type: "interactive",
        card: {
          config: {
            wide_screen_mode: true,
          },
          header: {
            template: "green",
            title: {
              tag: "plain_text",
              content: options.cardTitle || "OpenCode 任务已完成",
            },
          },
          elements: [
            {
              tag: "div",
              text: {
                tag: "lark_md",
                content: message.replace(/\n/g, "\n"),
              },
            },
            {
              tag: "action",
              actions: [
                {
                  tag: "button",
                  text: {
                    tag: "plain_text",
                    content: "打开 Session",
                  },
                  url: options.sessionUrl,
                  type: "primary",
                },
              ],
            },
          ],
        },
      }
    : {
        msg_type: "text",
        content: {
          text: `[OpenCode]\n${message}`,
        },
      };

  try {
    const response = await fetch(webhook, {
      method: "POST",
      headers: {
        "content-type": "application/json",
      },
      body: JSON.stringify(body),
    });

    if (!response.ok) {
      return false;
    }

    const result = await response.json().catch(() => undefined);
    return !result || result.code === 0 || result.StatusCode === 0;
  } catch {
    return false;
  }
}

function shouldNotifyFeishu(sessionID) {
  return !activeSessionID || activeSessionID !== sessionID;
}

async function notify(message, options = {}) {
  return shouldNotifyFeishu(options.sessionID) ? notifyWithFeishu(message, options) : false;
}

export const TaskFinishPopupPlugin = async ({ directory, serverUrl }) => {
  return {
    event: async ({ event }) => {
      if (event.type === "tui.session.select") {
        activeSessionID = event.properties.sessionID;
        return;
      }

      if (event.type === "session.status") {
        const { sessionID, status } = event.properties;

        if ((status.type === "busy" || status.type === "retry") && !sessionStartTimes.has(sessionID)) {
          sessionStartTimes.set(sessionID, Date.now());
        }

        return;
      }

      if (event.type !== "session.idle") {
        return;
      }

      const { sessionID } = event.properties;
      const startedAt = sessionStartTimes.get(sessionID);
      sessionStartTimes.delete(sessionID);

      const durationMs = startedAt ? Date.now() - startedAt : 0;
      if (!startedAt || durationMs < MIN_NOTIFY_DURATION_MS) {
        return;
      }

      const sessionContext = await getSessionContextWithRetry(serverUrl, directory, sessionID);
      const sessionTitle = sessionContext.title || sessionID;
      const assistantSummary = sessionContext.assistantText;
      const sessionUrl = buildSessionUrl({ directory, serverUrl, sessionID });
      const messageLines = [
        `会话: ${sessionTitle}`,
        `Session ID: ${sessionID}`,
        `耗时: ${Math.round(durationMs / 1000)} 秒`,
      ];

      const sshSourceLabel = getSSHSourceLabel();
      if (sshSourceLabel) {
        messageLines.splice(2, 0, `SSH 来源: ${sshSourceLabel}`);
      }

      if (assistantSummary) {
        messageLines.push(`回复摘要: ${assistantSummary}`);
      }

      await notify(messageLines.join("\n"), {
        sessionID,
        sessionUrl,
        cardTitle: `OpenCode ${sessionTitle} 已完成`,
      });
    },
  };
};
```

## 第三步：创建本地配置文件

把本地配置保存到：

```text
~/.config/opencode/plugins/task-finish-popup.local.json
```

```json
{
  "feishuWebhook": "https://open.feishu.cn/open-apis/bot/v2/hook/your-webhook-id",
  "publicBaseUrl": "http://127.0.0.1:4096"
}
```

这个文件里可以放真实 webhook；如果你不想落盘，也可以只保留示例值，改用环境变量。

## 第四步：配置飞书 webhook

你可以二选一：

### 方案 A：写到本地配置文件

在 `~/.config/opencode/plugins/task-finish-popup.local.json` 中填写：

```json
{
  "feishuWebhook": "https://open.feishu.cn/open-apis/bot/v2/hook/your-webhook-id",
  "publicBaseUrl": "http://127.0.0.1:4096"
}
```

### 方案 B：使用环境变量

在你的 shell 配置文件里加入：

```bash
export OPENCODE_FEISHU_WEBHOOK='你的飞书 webhook'
```

如果你用的是 `zsh`，通常写到：

```bash
~/.zshrc
```

然后执行：

```bash
source ~/.zshrc
```

插件会优先读取环境变量；如果没有，再回退到本地配置文件。

## 第五步：把 `opencode` 换成自动启动 Web 的包装脚本

把原始 `opencode` 重命名为 `opencode-real`，再把下面脚本保存为你的 `opencode` 命令。

```bash
#!/usr/bin/env bash

set -euo pipefail

REAL_OPENCODE="$HOME/opencode/bin/opencode-real"
WEB_HOST="127.0.0.1"
WEB_PORTS=(4096 4097 4098 4099 4100)
WEB_PORT="${WEB_PORTS[0]}"
WEB_URL="http://${WEB_HOST}:${WEB_PORT}"
WEB_LOG="/tmp/opencode-web.log"
WEB_PID_FILE="/tmp/opencode-web.pid"
WEB_PORT_FILE="/tmp/opencode-web.port"

is_web_ready() {
  node -e '
    const http = require("http");
    const req = http.get(process.argv[1], (res) => {
      process.exit(res.statusCode && res.statusCode < 500 ? 0 : 1);
    });
    req.setTimeout(1000, () => {
      req.destroy();
      process.exit(1);
    });
    req.on("error", () => process.exit(1));
  ' "$1" >/dev/null 2>&1
}

is_port_free() {
  node -e '
    const net = require("net");
    const host = process.argv[1];
    const port = Number(process.argv[2]);
    const server = net.createServer();
    server.once("error", () => process.exit(1));
    server.listen(port, host, () => server.close(() => process.exit(0)));
  ' "$WEB_HOST" "$1" >/dev/null 2>&1
}

select_web_port() {
  local saved_port url port

  saved_port="$(cat "$WEB_PORT_FILE" 2>/dev/null || true)"
  if [[ -n "$saved_port" ]]; then
    url="http://${WEB_HOST}:${saved_port}"
    if is_web_ready "$url"; then
      echo "$saved_port"
      return
    fi
  fi

  for port in "${WEB_PORTS[@]}"; do
    url="http://${WEB_HOST}:${port}"
    if is_web_ready "$url"; then
      echo "$port"
      return
    fi
  done

  for port in "${WEB_PORTS[@]}"; do
    if is_port_free "$port"; then
      echo "$port"
      return
    fi
  done

  echo "${WEB_PORTS[0]}"
}

ensure_web() {
  local pid
  WEB_PORT="$(select_web_port)"
  WEB_URL="http://${WEB_HOST}:${WEB_PORT}"

  if is_web_ready "$WEB_URL"; then
    printf '%s' "$WEB_PORT" >"$WEB_PORT_FILE"
    return
  fi

  BROWSER=true nohup "$REAL_OPENCODE" web --hostname "$WEB_HOST" --port "$WEB_PORT" >>"$WEB_LOG" 2>&1 &
  pid=$!
  printf '%s' "$pid" >"$WEB_PID_FILE"
  printf '%s' "$WEB_PORT" >"$WEB_PORT_FILE"

  for _ in $(seq 1 20); do
    if is_web_ready "$WEB_URL"; then
      return
    fi
    sleep 0.5
  done
}

if [[ "${1-}" != "web" ]]; then
  ensure_web
  export OPENCODE_PUBLIC_URL="${OPENCODE_PUBLIC_URL:-$WEB_URL}"
fi

exec "$REAL_OPENCODE" "$@"
```

## 第六步：验证

### 1. 验证 Web

```bash
curl -I http://127.0.0.1:4096
```

返回 `200` 即可。

### 2. 验证飞书 webhook 环境变量

```bash
printenv OPENCODE_FEISHU_WEBHOOK
```

能打印出 webhook 即可。

### 3. 验证提醒

启动 `opencode`，执行一个超过 1 分钟的任务。

预期收到飞书卡片：

- 标题格式：`OpenCode <AI标题> 已完成`
- 正文包含会话标题、`sessionID`、耗时和简短回复摘要
- 如果 `publicBaseUrl` 可访问，则有“打开 Session”按钮
- 不会再出现本地终端或桌面通知回退

## 常见问题

### 1. 飞书收到的还是旧格式

说明后台 `opencode web` 还是旧进程，需要重启。

### 2. 飞书按钮打不开

说明 `publicBaseUrl` 不可访问。

SSH 场景下通常应使用：

```json
{
  "publicBaseUrl": "http://127.0.0.1:4096"
}
```

并确保本机做了 SSH 端口转发。

### 3. 没有收到飞书提醒

优先检查：

- `OPENCODE_FEISHU_WEBHOOK` 是否已导出，或本地配置文件中的 `feishuWebhook` 是否已填写
- 当前任务是否超过 30 秒
- 完成的 session 是否正好是你当前正在看的 session
- 当前不会再回退到本地通知；飞书失败时会直接静默跳过

### 4. 为什么不是纯 skill

因为 OpenCode 的任务完成提醒依赖插件事件监听，`skill` 本身只负责说明和组织规则，不能单独监听任务生命周期。
