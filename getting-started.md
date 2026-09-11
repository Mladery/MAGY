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

> 如果你的账号尚未转正，会被要求先输入注册码。注册码获取方式见 [注册码文档](/wiki/regcodes.md)。

## 2.1 两个常用端点

Base URL 填 `https://aclimladery.dpdns.org/v1` 之后：

| 用途 | 端点 | 说明 |
| --- | --- | --- |
| 文本 / 对话 | `POST /v1/chat/completions` | **酒馆（SillyTavern）和通用 AI 客户端只要填了 Base URL 就会自动补上** `/chat/completions`，不用手写 |
| 生图 | `POST /v1/images/generations` | 图片生成接口，参数按 OpenAI 兼容格式 |

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
- **账号未注册**：授权成功后仍要求注册码，说明你的账号尚未转正。
