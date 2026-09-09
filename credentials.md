# 凭证（AGY 凭证）

AGY 公益站通过凭证池轮询 Google Antigravity 账号额度。凭证来自社区成员共享。

## 最重要的一条

**不要把 GCLI 凭证上传给 AGY 公益站！AGY 公益站不收 GCLI 凭证！**

- GG 公益站里的"下载凭证"全部都是 GCLI。
- GCLI 凭证与 AGY 凭证格式不同、项目归属不同，上传后无法被代理识别。

## 推荐方式：OAuth 授权

在门户「AGY 凭证」区块点击「发起授权」：

1. 按照页面提示在新标签页完成 Google OAuth。
2. 把回调产生的 URL 粘贴回来提交。
3. 网关会自动认领并转换凭证、立即验活，成功后显示账号类型（free / pro / elite 等）。

OAuth 授权的好处：

- 不用手工导出 JSON，格式兼容有保障。
- 上传即验活，避免 `missing project_id` / `missing refresh_token` 等问题。

## 手工上传凭证

也支持上传 `.json` 文件（≤2MB）：

- 网关会自动把 Gemini CLI `authorized_user` 格式转换为 `antigravity` 格式。
- `type` 已经是 `antigravity` 的凭证原样透传。
- 只上传**AGY（Antigravity）**凭证！

## 上传后的规则

- 凭证内容仅**上传者本人**可下载、禁用、删除；他人看不到你的文件。
- 同名凭证不可覆盖：更新需先删除旧件再上传。
- 共享池目前合计上限 **50 个**凭证。
- 上传后立即验活：
  - 有效：显示账号类型与邮箱。
  - 无效：自动禁用，不参与配额，并返回错误码（如 `invalid_grant`、`missing_refresh_token`、`loadCodeAssist_failed`）。

## missing project_id 怎么办？

`missing_project_id` 表示凭证无法解析出 Google 项目。我们已经做过修复；如果多次重试仍失败：

- 优先改用 OAuth 授权。
- 或更换账号重新授权。
- 实在不行可带图提问，但有概率无法处理。

## 封号风险

上传凭证有概率导致 Google 账号被封，请慎重。

可能后果：**AGY + GCLI 一起被 ban**。

## 凭证报错官方话术

1. 请按首页标注图片尝试，优先 OAuth 授权。
2. 试了/确定是 AGY 凭证了/还不行？换梯子多刷新几次。
