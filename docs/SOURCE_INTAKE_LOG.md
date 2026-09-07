# 信源接入日志（Source Intake Log）

> 伯乐 Skill 的规矩：**先判断清楚，再接入**。每个源都必须留下判定依据，
> 接完还要复核，不能接完就不管。
>
> 负责人：杨杨 / 维护：发财
> 首次接入：2026-09-07

## 关注方向（2026-09-07 杨杨订正）

之前我把游戏当主业，搞错了。真实需求是：

1. **谁发了什么模型**——测试怎么样、跑分多少
2. **谁拿新模型做了什么**——应用落地，不是八卦
3. **AI 圈人物声音**——Tibo(Thibault Sottiaux)、马斯克、Sam Altman、Karpathy、Dan Shipper、Simon Willison 等
4. **SaaS / 独立开发者**的动静
5. 游戏 / SEO 只是顺带

明确不要：诉讼、融资收购、版权纠纷这类行业八卦。

## 判定标准

用 `skills/ai-news-radar-scout/scripts/probe_sources.py` 实测：

| 指标 | 阈值 | 含义 |
|---|---|---|
| AI 占比 | ≥30% 接受 / 10-30% 观察 / <10% 跳过 | 最近 N 条里跟 AI 相关的比例 |
| 停更检测 | >90 天没更新 = 死源 | **只看占比会漏掉停更源** |
| 抓不到 | 无可用 RSS | 换路子或放弃 |

**重要教训：脚本判定必须人工复核。** 垂直站（如 AI and Games）整站讲游戏 AI，
但标题里常不写 "AI" 两个字，关键词判定会系统性低估它。

## 已接入的源（25 个）

### 模型官方与研究机构

| 源 | AI 占比 | 最新更新 | 判定 |
|---|---|---|---|
| OpenAI News | 79.8% | 活跃 | 接受 |
| Hugging Face Blog | 48.5% | 活跃 | 接受 |
| Google DeepMind | 42% | 活跃 | 接受 |
| Google Research | 39% | 3 天前 | 接受 |
| Microsoft Research | 40% | 6 天前 | 接受 |
| Google AI Blog | — | 上游默认源 | 沿用 |
| Microsoft AI Blog | — | 上游默认源 | 沿用 |
| NVIDIA Generative AI | — | 上游默认源 | 沿用 |

### AI 圈人物声音

| 源 | AI 占比 | 判定 |
|---|---|---|
| Sebastian Raschka（大模型技术解读） | **90%** | 接受 |
| Simon Willison | **73%** | 接受 |
| Latent Space | **60%** | 接受 |
| One Useful Thing（Ethan Mollick） | **55%** | 接受 |
| Import AI（前 OpenAI 政策总监） | **30%** | 接受 |
| 宝玉（中文 AI 博主） | — | 沿用上游 |

### SaaS 与独立开发者

| 源 | AI 占比 | 判定 |
|---|---|---|
| Lenny's Newsletter | **40%** | 接受 |
| Product Hunt | **36%** | 接受 |
| Hacker News 首页 | 10% | 观察（AI 圈重要来源，值得试） |

### 科技媒体 / 顺带

| 源 | AI 占比 | 判定 |
|---|---|---|
| Wired AI | — | 沿用上游 |
| InfoQ CN | — | 沿用上游 |
| AI and Games（游戏 AI 垂直站） | 25% | 观察 → **人工改判接受**（垂直站被系统性低估） |
| Search Engine Journal（SEO） | 25% | 观察 |

### 实验组（本机测不了，Actions 上验证）

本机代理（`127.0.0.1:7897`）挡住了 Google News，测不了。
这 4 个源用来补 **Anthropic / Meta / xAI 三家没有官方 RSS 的厂商**：

- GNews: Anthropic Claude
- GNews: Meta Llama
- GNews: xAI Grok
- GNews: LLM benchmark

**验证方式**：跑完看 `data/source-status.json`，FAIL 的下一轮直接删。

## 判定为「跳过」的源（留档，避免以后重复踩）

| 源 | 实测 | 为什么跳过 |
|---|---|---|
| **Qwen 官方博客** | AI 占比 77.3%，看着很香 | **最后更新 349 天前**——死源 |
| **SemiAnalysis** | 30% | 停更 355 天 |
| **Unity 官方博客** | 50 条里 3 条 = **6%** | 更新很勤但 94% 是噪音 |
| **GamesIndustry.biz** | 100 条里 2 条 = **2%** | 噪音站 |
| Mistral AI 官方 | 8.5% | 占比太低 |
| HN 高分 AI 切片 | 0% | URL 参数没生效，命中为空 |

**Unity 那 6% 就是伯乐存在的理由**：订它等于每天帮你收 47 条噪音，就为里面 3 条有用的。

## 抓不到的源（没有可用 RSS）

Anthropic、Meta AI、xAI、Cohere（模型官方，四家全无 RSS）
Indie Hackers、Starter Story、MicroConf、Rob Walling、Pieter Levels（独立开发者）
Epoch AI、Artificial Analysis（评测）

→ 独立开发者这块**目前仍是空白**，只有 Lenny's + Product Hunt + HN 撑着。待补。

## 复核计划

**一周后（2026-09-14）必做：**

1. 拉 `data/source-status.json`，看每个源是 OK 还是 FAIL
2. FAIL 的源：直接删，不留情
3. OK 但 0 条的源：说明抓得到但没料，降级到观察
4. 检查新增源的**实际产出条数**，而不是只看"能不能抓到"
5. 把结果回填到这个文件的"复核记录"章节

## 首轮 Actions 实测（2026-09-07 08:02 UTC）

跑通了。**25 个源，24 个 OK，1 个 FAIL，零条源 0 个。**
原始抓取 4159 条 → 24 小时窗口内 175 条（上游公共雷达同期约 155 条）。

### 各源实抓条数

| 条数 | 源 |
|---|---|
| 1173 | OpenAI News |
| 859 | Hugging Face Blog |
| 100 | GNews: Anthropic Claude / LLM benchmark / Meta Llama / xAI Grok |
| 100 | Google DeepMind / Google Research / NVIDIA |
| 50 | Product Hunt、宝玉 |
| 30 | Simon Willison |
| 20 | AI and Games、Google AI Blog、HN 首页、InfoQ CN、Latent Space、Lenny's、One Useful Thing、SEJ、Raschka |
| 10 | Microsoft AI Blog、Microsoft Research、Wired AI |
| **FAIL** | Import AI（Substack 403） |

### 关键结论

1. **实验组全活了。** Google News 四个切片各抓 100 条——本机代理挡住的
   Anthropic / Meta / xAI / 跑分消息，在 Actions 机房里抓得好好的。
   三家没官方 RSS 的厂商，这条路补上了。
2. **Import AI 403**：Substack 挡了 Actions 的请求头。本机带 UA 能抓到，
   已把 URL 改成带斜杠的 `/feed/`，等下一轮验证；再失败就换 `importai.net/feed`，
   还不行就删掉（30% 占比，不是核心源）。
3. **AI Breakfast 仍然 403**：这是上游自带的源，一直挂着，不是我们的锅。

## 复核记录

### 2026-09-07 首轮

- [x] 25 源接入，24 成功
- [x] 修掉 Import AI 的 403（改 URL 待验证）
- [ ] 2026-09-14：看一周后各源的**实际产出条数**，不是只看能不能抓到。
      抓得到但天天 0 条的源，一样要砍。
