# MAGY 公益站 · Wiki 概览

欢迎来到 MAGY 公益站知识库。左侧导航可以切换到不同主题，顶部搜索框支持按标题快速过滤文档。

> 提问前请先阅读本页快速索引与对应分类文档，大部分问题都能在这里找到答案。

## 快速索引

| 主题 | 说明 | 文档 |
| --- | --- | --- |
| 🚀 快速开始 | 登录、领取 API Key、CLI 接入 | [getting-started.md](/wiki/getting-started.md) |
| 🔑 凭证 | AGY / Freebuff 两种 OAuth 授权、凭证上传、GCLI 区别 | [credentials.md](/wiki/credentials.md) |
| 🧠 模型与配额 | 可用模型、配额规则、Opus 特殊配额 | [models-and-quota.md](/wiki/models-and-quota.md) |
| 🎨 Qwen 生图 | 限时开放、「推车」进度、未转正也能领临时授权 | [qwen-image.md](/wiki/qwen-image.md) |
| 🎲 恶魔轮盘赌 | 玩法、道具、0 配额局、注册码掉落 | [roulette.md](/wiki/roulette.md) |
| 🎫 注册码 | 获取渠道、使用位置、常见错误 | [regcodes.md](/wiki/regcodes.md) |
| 🧹 账号保留与清退 | 每周五 23:00 清退从未调用过模型的账号 | [retention.md](/wiki/retention.md) |
| ⚠️ 报错速查 | 400 / 403 / 502 / missing project_id 等 | [errors.md](/wiki/errors.md) |
|  风控与安全 | 限流、防贩子、封禁规则 | [risk-control.md](/wiki/risk-control.md) |
| 📢 更新日志 | 近期功能与规则调整 | [updates.md](/wiki/updates.md) |

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
3. 请优先使用 OAuth 授权：**要 AGY 凭证就用「🔑 AGY OAuth 授权」卡片，要 Freebuff 凭证就用「🆓 Freebuff OAuth 授权」卡片**。两张卡相邻、各自独立：AGY 卡的链接是 `accounts.google.com`、需要粘贴回调；Freebuff 卡的链接是 `freebuff.com`、**不需要粘贴任何内容**。

详见 [凭证文档](/wiki/credentials.md)。

### Q4：403 报错 "The caller does not have permission" 怎么办？ {#q4}

轮询到的共享凭证本身已失效（账号被上游限制 / 项目无权限）。这类凭证被判定后会**自动永久移出轮换**，所以同一个坏凭证不会再反复出现。

**解决**：重新 roll 一次；如果同一条回复连续 403，请带图反馈。

### Q5：注册码哪里有？又在哪里用？ {#q5}

- DC 授权后若未注册，会提示输入注册码。
- 获取渠道：站主不定期发放 + 恶魔轮盘赌 0 配额局随机掉落。

详见 [注册码文档](/wiki/regcodes.md)。

### Q6：我很久没用，账号会被清退吗？ {#q6}

**每周五 23:00（北京时间）** 检查一次，按 **7 天一个周期**判定。每个周期内**满足任意一条**就能再留一个周期：

- 用本站发给你的 Key **成功调用过模型**（本周期内，一次即可）；
- **提供过可用凭证**（本周期内 OAuth 授权或上传、验活成功）。

两条都不满足的账号会被清退 —— API Key 立即失效，名下上传的凭证文件一并删除。想回来需要**重新获取注册码**兑换后才能使用。

注册不满 7 天会自动缓冲；只领注册码、只登录门户、请求全部失败，都不算"用过"。

门户账号区块会直接显示你的留存状态。详见 [账号保留与清退](/wiki/retention.md)。

### Q7：Qwen 生图为啥调不通 / 说「当前未开放」？ {#q7}

那不是你的 Key 有问题，也不是被限流——**生图模型是限时开放的**，由站主按「推车」模式手动开启：大家每成功出一张图就把进度往前推一点，推过 **33% / 66% / 100%** 检查点就延长开放时间，时间耗尽自动关闭并重置。

- 想看现在开没开：门户页**第一栏**的「Qwen模型调用贡献进度」进度条和倒计时。
- 调用未开放时会返回 **429**，`error_code` 是 `model_gate_closed`，并把当前进度一起告诉你。
- **生图不占用你每天的对话额度**，与三族配额完全独立。
- **还没转正也能用**：推车开放期间可在推车卡片里点「🔑 领取临时授权」，拿一把只能生图的临时 Key，本轮结束统一销毁。

详见 [Qwen 生图](/wiki/qwen-image.md)。

## 提问前请准备好

- **酒馆 RP 相关问题**：请附上酒馆插件、报错日志截图/粘贴。
- **凭证上传问题**：请截图首页顶部提示信息。
