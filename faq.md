# MAGY 公益站 · Wiki 概览

欢迎来到 MAGY 公益站知识库。左侧导航可以切换到不同主题，顶部搜索框支持按标题快速过滤文档。

> 提问前请先阅读本页快速索引与对应分类文档，大部分问题都能在这里找到答案。

## 快速索引

| 主题 | 说明 | 文档 |
| --- | --- | --- |
| 🚀 快速开始 | 登录、领取 API Key、CLI 接入 | [getting-started.md](/wiki/getting-started.md) |
| 🔑 凭证 | AGY 凭证上传、OAuth 授权、GCLI 区别 | [credentials.md](/wiki/credentials.md) |
| 🧠 模型与配额 | 可用模型、配额规则、Opus 特殊配额 | [models-and-quota.md](/wiki/models-and-quota.md) |
| 🎲 恶魔轮盘赌 | 玩法、道具、0 配额局、注册码掉落 | [roulette.md](/wiki/roulette.md) |
| 🎫 注册码 | 获取渠道、使用位置、常见错误 | [regcodes.md](/wiki/regcodes.md) |
| ⚠️ 报错速查 | 400 / 403 / 502 / missing project_id 等 | [errors.md](/wiki/errors.md) |
| 📢 更新日志 | 近期功能与规则调整 | [updates.md](/wiki/updates.md) |
|  风控与安全 | 限流、防贩子、封禁规则 | [risk-control.md](/wiki/risk-control.md) |

## 高频问题速答

### Q1：400 报错 "Requests ending with a model turn are not supported." 怎么办？ {#q1}

根本原因是你发送的文本中最后一条对话是 AI 回复结尾（assistant）。

**解决**：再发一条 `continue` 或一个空格，或编辑对话让最后一条不是 assistant。

### Q2：400 报错 "User location is not supported for the API use." 怎么办？ {#q2}

公益站代理轮询到了 Google 不支持的落地 IP 节点，不是你账号的问题。

**解决**：重新 roll。

### Q3：凭证上传失败怎么回事？ {#q3}

1. **不要把 GCLI 凭证上传给 AGY 公益站，AGY 公益站不收！**
2. GG 公益站里的"下载凭证"全部都是 GCLI。
3. 请优先使用 OAuth 授权提交凭证。

详见 [凭证文档](/wiki/credentials.md)。

### Q4：403 报错 "The caller does not have permission" 怎么办？ {#q4}

凭证池里有人上传了不支持 AGY 的凭证且没被验死，不关你的事。

**解决**：重 roll。

### Q5：注册码哪里有？又在哪里用？ {#q5}

- DC 授权后若未注册，会提示输入注册码。
- 获取渠道：站主不定期发放 + 恶魔轮盘赌 0 配额局随机掉落。

详见 [注册码文档](/wiki/regcodes.md)。

## 提问前请准备好

- **酒馆 RP 相关问题**：请附上酒馆插件、报错日志截图/粘贴。
- **凭证上传问题**：请截图首页顶部提示信息。
