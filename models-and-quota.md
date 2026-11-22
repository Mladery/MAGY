# 模型与配额

## 家族与路径

本站同时兼容多种协议风格，不同客户端可自由选择。

| 家族 | 兼容端点示例 | 备注 |
| --- | --- | --- |
| Gemini / Antigravity | `/v1/chat/completions`（OpenAI 兼容） | 主力渠道，凭证池轮询 |
| Claude | `/v1/messages`（Anthropic 兼容） | 可由 Gemini 网关映射 |
| OpenAI | `/v1/responses` / `/v1/chat/completions` | 兼容层 |
| Interactions | Gemini Interactions 协议 | 支持 CLI 交互调用 |
| 免费层 | `/v1/chat/completions` | `big-pickle` 与 `*-free` 系列，**不计入每日额度** |
| AI Studio 自备凭证 | `/v1/chat/completions` | `pg-` / `bd-` / `ap-` 前缀，**花你自己的 Google 额度** |

具体模型名请以门户/管理面板当前列表为准（模型注册表会自动热更新）。

## 配额基础规则

- 每个 Discord 账号有独立额度，按天统计。
- **完成 Discord 授权即可用**，基础额度 5 次/天（注册码机制已于 2026-11-20 关闭，不再需要转正流程）。
- 额度与凭证池轮询、共享池容量相关，繁忙时段可能拥堵。
- 共享池里**每份凭证有各自的 5 小时窗口**，用尽后要等上游重置；网关会主动避开剩余额度低于 10% 的凭证，见 [网关怎么挑凭证](#routing)。个人用量可在 `/quota` 查看 5 小时配额看板。

## 家族配额独立计算

两个家族（外加两组不计额度的通道）各自独立，用掉一个不会吃掉另一个：

| 家族 | 默认每日额度 | 贡献凭证加成 |
| --- | --- | --- |
| Gemini / 通用 | **5** | 免费凭证 +33 / Pro 凭证 +110 |
| Claude | **5** | 免费凭证 +10 / Pro 凭证 +30 |
| 免费层（`*-free` / `big-pickle`） | **不计入额度** | 不适用 |
| AI Studio 自备凭证（`pg-` / `bd-` / `ap-`） | **不计入额度** | 不适用 |

> 基础额度是 **5**，不是「不限量」。想往上加只有一条路：**贡献可用凭证**（免费凭证 Gemini +33 / Claude +10，Pro 凭证 Gemini +110 / Claude +30）。凭证必须是**活的**才计：禁用或验活失败的不算。
>
> 门户首页的「**今日配额用量**」按这条规则显示。额度耗尽后再调用该家族会返回 429（`account daily call limit reached`），换另一个家族或第二天再试。

## 网关怎么挑凭证 {#routing}

共享池是很多人的账号拼起来的，**每份账号的 5 小时窗口各自独立重置**。所以网关不是简单地轮询，而是按下面的顺序挑：

1. **先看额度再发请求**。只考虑「这个模型所属家族还有额度」的凭证；5 小时窗口剩余**低于 10%** 的凭证不再接新请求，留一点余量，避免一个请求在最后关头把窗口打穿。
2. **家族各记各的账**。Gemini 与 Claude 分开统计、分开路由，一个家族被抽干不会拖累另一个。
3. **同一段对话固定同一个凭证**（会话粘性，6 小时）。这样上游的提示词缓存才有机会命中，续写更快也更省。网关重启后这个绑定关系还在。
4. **全池都不行就直说**。如果这个家族**所有**凭证都低于 10%，请求在网关这一层就被拒掉，返回 `400 model_quota_unavailable` 并附上最早恢复时间 —— 不会送到上游再被退回来，也不会让你干等几十秒。

> 池子是社区贡献的，容量随时变化。繁忙时段某个家族被抽干、需要等窗口重置，是正常现象；换另一个家族、免费层，或用自己的 `pg-` 别名都能绕开。

## 不计额度的两组通道

这两组**完全不消耗**上面那两个家族每日次数，额度耗尽也照常可用：

1. **免费层**：模型名带 `-free` 后缀的，以及 `big-pickle`。这些走上游免费额度，本站只做转发。上游繁忙时可能失败或排队，属于正常现象。
2. **AI Studio 自备凭证**：模型名以 `pg-` / `bd-` / `ap-` 开头，后面跟你的凭证号。见下一节。

## AI Studio 自备凭证（`pg-` / `bd-` / `ap-`）

在门户「🧪 AI Studio 反代」里上传一次 Google 登录态，就能用**自己的账号**跑模型：

- **三通道**：`pg-` = Playground 额度，`bd-` = Build 额度，`ap-` = Gemini 网页端额度。三条各花各的，互不影响。
- **模型名形如** `pg-03a37edeae-gemini-3.8-flash`：`pg` 是通道、中间那串是你的凭证号、后面是模型名。这个凭证号对应你上传的那份登录态，**换登录态时凭证号不变**，所以配置里的模型名不用改。
- **按人隔离**：`pg-` 模型只有你自己看得见、调得动，别人即使知道名字也会被拒（`model_not_owned`）。
- **不占本站额度**：花的是你自己 Google 账号的额度，本站的每日 429 不会落到这上面。
- **每人最多 5 份**，登录态失效可原地更新。
- **能调哪些模型，取决于你的 Google 账号**：同一个 `pg-` 前缀下，不同账号看到的模型数不一样，付费档（见下一节）尤其如此。目录里有、你的号没权限时，调用会返回 403 `The caller does not have permission` —— 这是上游按账号发放的权限，不是本站拦你。

## gemini-4 的准入（重要） {#gemini4}

`gemini-4` 这一代是**按账号发放**的，本站无法代你开通。规则如下：

- **四个代号**：`argon` / `neon` / `carbon` / `hydrogen`。带 `-high` / `-xhigh` 后缀的是同一模型的不同思考档位。
- **免费档只能用 `hydrogen`**；`argon` / `neon` / `carbon` 需要**付费档**（Ultra 或已付费的 Pro 订阅）。实测同一账号、同一出口，`hydrogen` 连续 3 次全部成功，`argon` / `neon` / `carbon` 连续 3 次全部 403 `The caller does not have permission` —— 差别在账号权益，不在网络。
- **裸名 `gemini-4-*` 只有打上 `pro-paid` / `free-beta` 标签的账号才能调**，否则 403 `gemini4_credential_required`。标签由系统读你凭证的上游目录自动判定，不看你填了什么套餐。
- **`pg-` / `bd-` / `ap-` 别名不受这条限制**：它们走你自己的额度，你的号有没有权限由 Google 决定。
- **同档位内部也是分裂的**：官方文案写的是「Ultra **和**已付费的 Pro」，所以不能按「我是 Pro 就一定能用」推断，只能实测。

## DeepSeek

- **原「DeepSeek 家族 + Freebuff 凭证加成」已下线**（Freebuff 通道 2026-11-20 退役）。
- 现在能用的 DeepSeek 相关模型是**免费层**的 `deepseek-v4.1-flash-free`，**不占每日额度**，上游繁忙时可能失败。
- 原来的 `deepseek/deepseek-v4.1-flash` 已不再提供，客户端里如果还写着这个模型名会报模型不存在。

## 当前模型清单（27 个公共模型）

具体以 `/v1/models` 返回为准，下面是当前全貌：

| 类别 | 模型 |
| --- | --- |
| Gemini 3.x | `gemini-3-flash`、`gemini-3.1-flash-lite`、`gemini-3.5-flash-lite`、`gemini-3.8-flash-high`、`gemini-3.8-flash-tiered` |
| Gemini 4 | `gemini-4-argon-high`、`gemini-4-carbon-high`、`gemini-4-neon-xhigh`、`gemini-4-hydrogen-xhigh` |
| Claude | `claude-sonnet-4-6`、`claude-opus-4-6-thinking`、`claude-sonnet-5-5-high`、`claude-opus-5-5-high` |
| 生图 | `gemini-3.1-flash-image`、`qwen-image-2.1` |
| 其他 | `gpt-oss-120b-medium` |
| 免费层 | `big-pickle`、`deepseek-v4.1-flash-free`、`longcat-2.5-free`、`iceman-free`、`beam-free`、`flint-40b-free`、`nemotron-3.5-super-vl-free`、`nemotron-3.5-lightning-free`、`hy4-flash-free`、`tidepool-alpha-free`、`mimo-v2.6-flash-free` |

> 另有一批 `pg-` / `bd-` / `ap-` 开头的自备凭证别名，只对你自己的 Key 可见，不在这张公共清单里。


## Qwen 生图（限时开放）

除文本模型外，本站还提供 **Qwen-Image-2.1** 文生图：

- **端点**：`POST /v1/images/generations`（OpenAI 兼容格式）。
- **模型名**：`Qwen-Image-2.1`（也接受 `Qwen/Qwen-Image-2.1`）。
- **不占用每日对话额度**：与上表两个家族的额度完全独立，生图多少次都不会减少你的对话次数。
- **限时开放**：模型**默认关闭**，由站主按「推车」模式开启——每次成功出图给全体推进度，推过 **33% / 66% / 100%** 检查点就延长开放时间，时间耗尽自动关闭并重置。
- **只有成功出图才计数**；车没开时调用返回 **429**（`error_code` 为 `model_gate_closed`），并附带当前进度，这不是你的 Key 有问题。
- **没有可用 Key 也能试用**：推车开放期间可在门户页领取**只能生图**的临时 Key，本轮结束统一销毁。

完整玩法、临时授权与常见问题见 [Qwen 生图](/wiki/qwen-image.md)。

## Opus 系列特殊配额

- 经核实：**只有 Pro 凭证**才能调用 Opus 系列模型。
- 我们已收缩凭证池调用范围，大幅缓解 Opus 的 403。
- 代价：**Opus 系列模型消耗的 Claude 配额为 5 倍**。

## 模型名称与 alias

- 系统支持 alias 映射，方便不同客户端使用习惯。
- 拉不出模型时，先确认模型名与当前注册表一致（可查看门户提示或问 AI）。

## 常见模型相关报错

| 报错 | 含义 | 怎么办 |
| --- | --- | --- |
| `User location is not supported for the API use.` | 轮到的节点地区不受支持 | 重 roll |
| `Requests ending with a model turn are not supported.` | 最后一条是 assistant | 发 `continue` 或空格 |
| `gemini-3.1-flash-image` 报错 | 端点用错 | 改走 `/v1/images/generations` |
| 403 `gemini4_credential_required` | 你的账号没有 gemini-4 标签 | 只能用 `hydrogen`，或走自己的 `pg-` 别名 |
| 403 `The caller does not have permission` | 账号本身没有这个模型的权限（常见于付费档） | 不是故障；换 `hydrogen` 或用自己的凭证 |
| 403 `model_not_owned` | `pg-` 别名的凭证号不是你的 | 从 `/v1/models` 复制自己那条，别手写 |
| 403 `model_not_allowed` | 这把 Key 被限定在白名单内 | 用白名单里的模型，或换主 Key |
| 401 `未找到凭证 cred_xxx` | 别名指向的凭证已被删除 | 刷新 `/v1/models` 拿新的别名 |
| 429 `account daily call limit reached` | 该家族当日额度用完 | 换家族 / 等次日 / 调免费层模型 |
| 429 `model_gate_closed` | 生图「推车」当前没开 | 看门户第一栏的进度与倒计时 |


## 提示词缓存（为什么续写更省）

- 上游按「**账号 + 模型 + 上下文前缀**」缓存已经处理过的内容，命中部分更快、计费更省。
- 本站已开启**会话粘性**：同一段对话固定由同一个凭证服务，缓存才有机会命中。绑定关系落在磁盘上，**网关重启也不会打散**。
- 因此：**在同一段对话里继续**通常比每次新建会话更快也更省；每次都粘贴不同的超长内容则基本无法命中缓存。
- 换模型会重新算：`gemini-3.8-flash-high` 换到 `gemini-3-flash` 属于另一份缓存，第一句总是慢的。

更多见 [报错速查](/wiki/errors.md)。
