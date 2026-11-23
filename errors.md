# 报错速查

快速定位常见报错。若下列内容不能解决，请按群内格式带图提问。

## HTTP 400

### `model_quota_unavailable`

- 原因：这次请求要用的那个家族（Gemini 或 Claude），**全池凭证的剩余额度都低于 10% 的路由地板**。网关在发请求之前就拒绝了，不会空转几十秒，也不消耗你的每日次数。
- 文案长这样：`model gemini-3.8-flash-high is unavailable: every credential with Gemini quota is below the 10% routing floor until 2026-11-23T05:00:00Z`。后面的 `until` 是**最早**一批凭证的 5 小时窗口重置时刻。
- 解决：等 `until` 之后重试；或者改走下面任意一条，它们都不看共享池的额度：
  - 另一个家族（Gemini 换 Claude，反之亦然）；
  - 免费层（`big-pickle`、`*-free`）；
  - 自己的 `pg-` / `bd-` / `ap-` 别名（花你自己的 Google 额度）。

> 这个和你自己的每日额度是两回事。你自己额度用完是 **429** `account daily call limit reached`；池子见底是 **400** `model_quota_unavailable`。

### "Requests ending with a model turn are not supported."

- 原因：发送文本最后一条对话是 AI 回复结尾（assistant）。
- 解决：发一条 `continue` 或空格；或编辑对话去掉结尾 assistant。

### "User location is not supported for the API use."

- 原因：公益站代理轮询到 Google 不支持的落地 IP 节点。
- 解决：不是你的问题，重新 roll。

### gemini-3.1-flash-image 调用报错

- 解决：URL 后缀改为 `/v1/images/generations`。

## HTTP 403

### "The caller does not have permission"

- 原因有两种，看模型名区分：
  - **裸名 `gemini-4-*`**：你的账号没有这一档的权益（免费档只开放 `hydrogen`）。见 [gemini-4 的准入](/wiki/models-and-quota.md#gemini4)。
  - **`pg-` / `bd-` / `ap-` 别名**：**你自己的** Google 账号没有这个模型的权限。目录里有不代表你的号有权限，同一凭证下 `hydrogen` 能过而 `argon` 403 是正常现象。
  - **共享池的普通模型**：轮到的共享凭证本身已失效（账号被上游限制、项目无权限）。这类凭证现在会在**请求当场**被判死、永久移出轮换，不需要等下一轮验活，所以同一个坏账号不会反复出现。
- 解决：重新 roll 一次；若是自己的凭证，换 `hydrogen` 或升级账号。

### `gemini4_credential_required`

- 原因：`gemini-4-*` 这一代**按账号发放**，你的账号没有 `pro-paid` / `free-beta` 标签。
- 现状：标签由系统读你凭证的上游目录自动判定，不能自己填。**免费档目前没有任何账号能调 gemini-4**。
- 解决：用 `gemini-4-hydrogen-xhigh`；或用自己的 `pg-` 别名（不受这条限制）；同一账号的 `gemini-3.8-flash-high` / `gemini-3-flash` 仍然正常。

### `model_not_owned`

- 原因：`pg-` / `bd-` / `ap-` 别名里的**凭证号不是你的**。
- 解决：从 `/v1/models` 复制自己那条，不要手写或抄别人的。

### `model_not_allowed`

- 原因：这把 Key 被限定了模型白名单（只有特定用途的 Key 会这样）。
- 解决：用白名单里的模型，或换回你的主 Key。

### "VALIDATION_REQUIRED" / "SUBSCRIPTION_REQUIRED" / 账号不满足 Code Assist 条件

- 原因：凭证对应账号自身状态问题（需要人工重新验证、没有有效订阅、不满足个人版 Code Assist 条件）。
- 解决：与你无关，系统会自动禁用该凭证，重 roll 即可。

## HTTP 401

### `Invalid API key`

- 原因：这把 Key 已经不在网关的授权列表里。2026-11-24 全站整体吊销过一次 Key，旧 Key 全部作废；也可能是管理员单独重置了你的 Key。
- 解决：退出登录，重新走一次 Discord 登录（需要通过身份组校验），门户会发一把新 Key。旧 Key 不会恢复。

### `未找到凭证 cred_xxx，请先上传`

- 原因：别名里的凭证号指向的那份登录态已经被删除（你自己删的，或管理员清理）。
- 解决：刷新 `/v1/models` 拿新的别名；如果 `pg-` 模型全都不见了，去门户重新绑定登录态。

## HTTP 429

### `account daily call limit reached for gemini` / `... for claude`

- 原因：该家族的当日额度用完（基础 5 次 + 凭证加成）。
- 解决：换另一个家族、等次日，或改用 `*-free` 免费层。**`pg-` 别名与免费层不受这条限制。**

### `model_gate_closed`

- 原因：生图模型的「推车」当前没开。
- 解决：看门户第一栏的进度与倒计时，不是你的 Key 有问题。

## HTTP 502

- 通常是网关/回源异常或后端暂时重启中。
- 先刷新几次；持续出现请带图反馈。

## 凭证相关报错

### missing project_id

- 凭证无法解析 Google 项目。
- 已修复过；多次失败建议改用 OAuth 授权或更换账号。

### missing refresh_token

- 凭证缺少 refresh_token，无法验活。
- 优先使用 OAuth 授权提交。

### invalid_grant

- refresh_token 失效或已吊销。
- 重新走 OAuth 授权。

### loadCodeAssist_failed

- 验活调用了 loadCodeAssist 失败。
- 可能凭证无效或临时网络问题，稍后重试。

### 凭证被自动禁用（禁用原因以 403 开头）

- 原因：该凭证调用上游被拒（`PERMISSION_DENIED` / 需重新验证 / 无有效订阅 / 账号被限制），系统会**永久禁用**并保留原因，避免它继续被轮询、拖慢所有人。
- 判定时机：**不只是验活时**。对话、流式对话、生图这几条请求路径上，只要上游明确拒绝，当场就判死并移出轮换。
- **不会误判**：网络超时、连接中断、5xx、429 都只算「这次没测到」，不会因此禁用你的凭证。
- 解决：门户「我的凭证」可以查看禁用原因；换一个健康账号重新 OAuth 授权，或删除这条凭证。若你确信是上游临时抽风，把禁用原因截图发群里，管理员可以复核后恢复。

## 酒馆（SillyTavern）问题

### 拉不出模型 / 重 roll 一直报错

逐项排查：

1. URL 加 `/v1` 了吗？Key 复制对了吗？换非流式试试。
2. 预设适配当前模型吗？聊天记录删了吗？多换几个试试。
3. 排除这些参数：

```text
presence_penalty
frequency_penalty
top_p
top_k
temperature
```

## 提问格式

- **酒馆 RP 问题**：请并发酒馆插件 + 报错日志粘贴/截图。
- **凭证上传问题**：请截图首页顶部提示信息。
