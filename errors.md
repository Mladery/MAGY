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

- 原因：凭证池有人上传了不支持 AGY 的凭证且没被验死。
- 解决：不关你的事，重 roll。

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
