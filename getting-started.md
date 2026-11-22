# 快速开始

本文介绍如何从零开始接入 MAGY 公益站。

## 1. 用 Discord 登录

- 打开 [MAGY 公益站](https://aclimladery.dpdns.org/) 首页，点击「使用 Discord 登录」。
- 只有指定 Discord 服务器（Guild）成员才能登录，可选校验身份组 Role。

## 2. 领取你的 API Key

登录后进入 `/portal` 控制台：

- 每个 Discord 账号有**专属 API Key**（`sk-...`），首次访问自动生成，后续登录保持不变。
- 点击眼睛图标显示 / 隐藏 Key，点击「复制」复制完整 Key。
- **API 地址（Base URL）：`https://aclimladery.dpdns.org/v1`**
- **备用地址：`https://api.aclimfast.dpdns.org/v1`**（与主入口完全等价，同一个源站、同一套 Key）

> Discord 授权通过后**直接可用**，不再需要注册码 —— 注册码机制已于 2026-11-20 关闭。

## 2.1 两个常用端点

Base URL 填 `https://aclimladery.dpdns.org/v1` 之后：

| 用途 | 端点 | 说明 |
| --- | --- | --- |
| 文本 / 对话 | `POST /v1/chat/completions` | **酒馆（SillyTavern）和通用 AI 客户端只要填了 Base URL 就会自动补上** `/chat/completions`，不用手写 |
| 生图 | `POST /v1/images/generations` | 图片生成接口，参数按 OpenAI 兼容格式 |

> **生图是限时开放的**（模型 `Qwen-Image-2.1`，默认关闭、由站主按「推车」模式开启），且**不占用每日对话额度**；没开的时候调用会返回 429。规则见 [Qwen 生图](/wiki/qwen-image.md)。

```text
Base URL: https://aclimladery.dpdns.org/v1
对话端点: /chat/completions      （客户端自动补全）
生图端点: /images/generations
API Key:  sk-你的Key
```

## 3. 在常见 CLI 工具中接入

### Claude Code

```bash
export ANTHROPIC_BASE_URL=https://aclimladery.dpdns.org
export ANTHROPIC_AUTH_TOKEN=sk-你的Key
claude
```

### Codex CLI

```bash
export OPENAI_BASE_URL=https://aclimladery.dpdns.org/v1
export OPENAI_API_KEY=sk-你的Key
codex
```

### OpenAI / Gemini 兼容客户端

```text
Base URL: https://aclimladery.dpdns.org/v1
对话端点: /chat/completions      （客户端自动补全）
生图端点: /images/generations
API Key:  sk-你的Key
```

### 双向兼容提示

- 本站同时提供 OpenAI 风格（`/v1/chat/completions`、`/v1/responses`）与 Anthropic 风格（`/v1/messages`）端点。
- Gemini 家族请使用 OpenAI 兼容端点或 Gemini 兼容端点，具体路径见 [模型与配额文档](/wiki/models-and-quota.md)。

## 4. 验证是否可用

最快的方式是在门户页面直接复制示例配置；遇到报错先查 [报错速查](/wiki/errors.md)。

## 常见坑

- **URL 少写 `/v1`**：多数 OpenAI 兼容客户端需要 Base URL 以 `/v1` 结尾。
- **Key 复制不完整**：确认复制的是完整的 `sk-...`，不要带首尾空格或隐藏的 `||` 标记。
- **额度只有 5 次**：Gemini / Claude 两个家族的基础额度都是 **5 次/天**，不是不限量；想加额度只能贡献可用凭证（免费凭证 Gemini +33 / Claude +10，Pro 凭证更多）。详见 [模型与配额](/wiki/models-and-quota.md)。
- **凌晨 02:00–06:00（北京时间）门户与 API 返回 503**：这是计划内的宵禁，不是站点挂了。管理面板与 Wiki 在这个时段仍可访问。
- **`gemini-4-*` 报 403**：这一代模型按账号发放，免费档只能用 `hydrogen`。详见 [gemini-4 的准入](/wiki/models-and-quota.md#gemini4)。
- **想要不限额度的模型**：用 `*-free` 后缀的免费层，或在门户上传自己的 AI Studio 登录态拿 `pg-` 别名（花自己的 Google 额度）。
