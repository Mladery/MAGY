# 模型与配额

## 家族与路径

本站同时兼容多种协议风格，不同客户端可自由选择。

| 家族 | 兼容端点示例 | 备注 |
| --- | --- | --- |
| Gemini / Antigravity | `/v1/chat/completions`（OpenAI 兼容） | 主力渠道，凭证池轮询 |
| Claude | `/v1/messages`（Anthropic 兼容） | 可由 Gemini 网关映射 |
| OpenAI | `/v1/responses` / `/v1/chat/completions` | 兼容层 |
| Interactions | Gemini Interactions 协议 | 支持 CLI 交互调用 |

具体模型名请以门户/管理面板当前列表为准（模型注册表会自动热更新）。

## 配额基础规则

- 每个 Discord 账号有独立额度，按天统计。
- 未转正的账号（只登录过、既没有可用凭证也没兑换过注册码）可下注额度为 0，门户里也不会显示今日配额用量；兑换注册码或提供有效凭证后才有额度。
- 额度与凭证池轮询、共享池容量相关，繁忙时段可能拥堵。
- 公共网关持续轮询大量凭证，配额面板可在 `/quota` 查看 5 小时配额看板。

## 家族配额独立计算

三个家族的每日额度各自独立，用掉一个不会吃掉另一个：

| 家族 | 默认每日额度 | 贡献凭证加成 |
| --- | --- | --- |
| Gemini / 通用 | 33 | 免费凭证 +33 / Pro 凭证 +110 |
| Claude | 15 | 免费凭证 +10 / Pro 凭证 +30 |
| DeepSeek | 20 | **Freebuff 凭证 +50 / 张**（只认 Freebuff，Antigravity 凭证不加） |

> 「Freebuff 凭证加成」只统计**活的 Freebuff 凭证**：禁用或验活失败的凭证不计；Antigravity（AGY）凭证虽然也属于免费档，但**不会**加到 DeepSeek 家族。

## DeepSeek（V4.1 Flash）

- 对外模型名：`deepseek/deepseek-v4.1-flash`（即本站所说的 V4.1 Flash）。
- 目前 DeepSeek 家族**只开放这一档模型**，其余上游模型暂不对外。
- **基础额度 20 次/天**，并且**每张「活的 Freebuff 凭证」再 +50**（例如 1 张 = 70，2 张 = 120）。加成**按凭证张数**叠加，只认 **Freebuff** 凭证：Antigravity 凭证属于免费档，但**不计入**这一族；禁用或验活失败的凭证同样不计。
- 该家族的并发单独限流（默认 3 路），与其他家族分开计数，不占用全局并发额度。

## Opus 系列特殊配额

- 经核实：**只有 Pro 凭证**才能调用 Opus 系列模型。
- 我们已收缩凭证池调用范围，大幅缓解 Opus 的 403。
- 代价：**Opus 系列模型消耗的 Claude 配额为 5 倍**。

## 模型名称与 alias

- 系统支持 alias 映射，方便不同客户端使用习惯。
- 拉不出模型时，先确认模型名与当前注册表一致（可查看门户提示或问 AI）。

## 常见模型相关报错

- `User location is not supported for the API use.` → 节点地区问题，重 roll。
- `Requests ending with a model turn are not supported.` → 对话结尾是 assistant，发 continue。
- `gemini-3.1-flash-image` 调用报错 → URL 后缀改为 `/v1/images/generations`。

## 提示词缓存（为什么续写更省）

- 上游按「**账号 + 模型 + 上下文前缀**」缓存已经处理过的内容，命中部分更快、计费更省。
- 本站已开启**会话粘性**：同一段对话固定由同一个凭证服务，缓存才有机会命中。
- 因此：**在同一段对话里继续**通常比每次新建会话更快也更省；每次都粘贴不同的超长内容则基本无法命中缓存。

更多见 [报错速查](/wiki/errors.md)。
