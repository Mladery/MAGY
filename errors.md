# 报错速查

快速定位常见报错。若下列内容不能解决，请按群内格式带图提问。

## HTTP 400

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

- 原因：轮询到的共享凭证**本身已失效**（账号被上游限制、项目无权限等）。
- 现状：这类凭证一旦被判定即**自动永久移出轮换**（并记录禁用原因），同一个坏凭证不会再反复被轮询到；偶发遇到通常是新进池的坏凭证刚被判定。
- 解决：重新 roll 一次；若同一条回复连续 403，请带图反馈。

### "VALIDATION_REQUIRED" / "SUBSCRIPTION_REQUIRED" / 账号不满足 Code Assist 条件

- 原因：凭证对应账号自身状态问题（需要人工重新验证、没有有效订阅、不满足个人版 Code Assist 条件）。
- 解决：与你无关，系统会自动禁用该凭证，重 roll 即可。

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
- 解决：门户「我的凭证」可以查看禁用原因；换一个健康账号重新 OAuth 授权，或删除这条凭证。

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
