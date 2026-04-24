# 浏览器上下文

Memoh 通过 **Browser Gateway** 给机器人无头浏览器能力。**Browser Context** 里存视口、语言、时区、是否模拟手机等。绑到机器人后，可用工具打开页面、点按、填表、截图、读 DOM 等。

---

## Browser Gateway

基于 **Playwright**。一个 context 像一份可复用的「浏览器侧配置」。

适合：跳站、点链、填表、读渲染后内容、出图或 PDF 等。

---

## 建上下文

侧栏 **Browser Contexts**：

1. **Add Browser Context**
2. 填 **Name**
3. **Create**

---

## 配字段

| 字段 | 说明 |
|------|------|
| **Name** | 展示名 |
| **Core** | `chromium`（默认）或 `firefox` |
| **Viewport Width / Height** | 像素 |
| **User Agent** | 可选 |
| **Device Scale Factor** | 可选 DPR |
| **Locale** | 如 `en-US`、`zh-CN` |
| **Timezone ID** | 如 `UTC`、`Asia/Shanghai` |
| **Is Mobile** | 手机行为 |
| **Ignore HTTPS Errors** | 坏证书站是否还进 |

可 **Edit**、**Delete**。

---

## 绑到机器人

1. **Bots** → 打开机器人
2. **General**
3. **Browser Context** 下拉选
4. 保存

之后调浏览器工具时会用这个 profile。

---

## 机器人侧

常见内置如：

- `browser_action`：导航、点击、填表、选、滚、多 tab、截图、PDF 等
- `browser_observe`：看当前页，给模型用

让机器人能操作**真页面**，而不只有静态 HTML 或搜索摘要。

---

## 浏览器核

镜像里可带 Chromium、Firefox 或两者。构建时由 `BROWSER_CORES` 决定。一键安装脚本里会问；手搓例如：

```bash
BROWSER_CORES=chromium docker compose --profile browser build browser
```

合法值：`chromium`、`firefox`、`chromium,firefox`（默认组合）。

---

## 接下来

- 记忆与内置模式：[内置记忆提供方](/zh/memory-providers/builtin.md)
- 长期记忆操作：[长期记忆](/zh/getting-started/memory.md)
