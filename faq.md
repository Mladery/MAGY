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
| 🧹 账号保留与清退 | 每周五 23:00 清退从未调用过模型的账号 | [retention.md](/wiki/retention.md) |
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

轮询到的共享凭证本身已失效（账号被上游限制 / 项目无权限）。这类凭证被判定后会**自动永久移出轮换**，所以同一个坏凭证不会再反复出现。

**解决**：重新 roll 一次；如果同一条回复连续 403，请带图反馈。

### Q5：注册码哪里有？又在哪里用？ {#q5}

- DC 授权后若未注册，会提示输入注册码。
- 获取渠道：站主不定期发放 + 恶魔轮盘赌 0 配额局随机掉落。

详见 [注册码文档](/wiki/regcodes.md)。

### Q6：我很久没用，账号会被清退吗？ {#q6}

**每周五 23:00（北京时间）** 检查一次：注册后**从未调用过模型**的账号会被清退 —— API Key 立即失效，名下上传的凭证文件一并删除。

- **调用过一次模型**（用本站发给你的 Key 成功生成过内容）→ **长期保留**。
- **本周期（7 天）内提供过可用凭证** → 保留**至本周期结束**，下个周期重新判定。
- **注册不满 7 天** → 自动缓冲。
- 只领注册码、只登录门户、请求全部失败，都不算"用过"。

门户账号区块会直接显示你的留存状态。详见 [账号保留与清退](/wiki/retention.md)。

## 提问前请准备好

- **酒馆 RP 相关问题**：请附上酒馆插件、报错日志截图/粘贴。
- **凭证上传问题**：请截图首页顶部提示信息。
