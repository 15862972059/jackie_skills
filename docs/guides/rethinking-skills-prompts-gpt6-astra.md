# 笔记：《Rethinking skills and prompts for GPT-6 Astra》

- **来源**：OpenAI 开发者博客（developers.openai.com）
- **作者**：Eric Provencher
- **发布日期**：2026-09-11
- **原文**：<https://developers.openai.com/blog/rethinking-skills-and-prompts-for-gpt-6-astra>
- **一句话主旨**：模型能力跃升后，为旧模型积累的技能描述、AGENTS.md 指令和任务提示词需要"减负"——上下文越省越好，措辞越信任越好，完成标准越明确越好。

> 本文件为个人学习笔记与要点摘录，非原文复制；引述仅代表摘录用途，版权归 OpenAI 所有。

## 写了什么（按原文四个小节）

### 1. Better skills（更好的技能）
- 技能爆发式堆积后，宿主（Codex）会**截断过长的 description**，模型反而看不清该选哪个技能；互相矛盾或过度强调的触发描述会导致加载无关技能。
- **描述要尽量短**且精确划定"何时用"。原文对照：
  - 差：`Create and validate Postgres schema migrations. Use when working with databases, queries, models, or persistence.`（碰任何数据库相关都会误触发）
  - 好：`Create and validate Postgres schema migrations. Use when adding or changing a migration, or reviewing its rollout.`（只在迁移场景触发）
- **渐进披露（progressive disclosure）**：读技能就占上下文、逼近压缩阈值。多工作流的技能应把根 SKILL.md 做成"最小路由"，指向支撑文档与脚本，让模型知道去哪找，而不被迫读用不到的内容。
- 旧时代"详尽菜谱/行程单"式步骤，如今可能束缚强模型发挥；仓库技能会被其它模型的 agent 读到，要考虑指令对谁成立。

### 2. Up-to-date AGENTS.md
- 常逐条自问「这条还需要吗」。反例：「每次编辑前读 architecture.md + database.md + deployment.md」——改个错别字也要全量预读，烧上下文、拖慢一切。
- 正解是**按需指路**：「改服务边界用 architecture.md、改 schema 用 database.md、准备部署看 deployment.md」。
- 旧模型要催促才跑测试，Astra 自发会做——重复指令造成**过度测试**。
- 反过来，可用 AGENTS.md **主动授权**确认安全的流程（示例：本地测试为一次性 fixture、无生产访问 → 直接跑、修到通过、重跑受影响用例，无需每步请示）。

### 3. Decision boundaries（决策边界）
- 为旧模型"越权"教训写下的强硬「必须先询问」条款，Astra 会**太当真**，在其实可以继续的地方停下。它是 OpenAI 称对齐最好的模型，不会自行做不安全的事——措辞应降级为信任式；但真实的安全边界仍然保留。

### 4. Persistence（执行力/防早停）
- 相比 GPT-5.6 Sol 的长驱直入，Astra 更容易在"第一版实现"就回来要 review。
- 解法：**开工前定义完成**。若任务含"跑起来、检查效果、修到通过"，就把它写进请求里；「第一版实现后停下等 review」这句话本身就会把停止点提前。想让它继续探索，就说明要探索什么、到哪为止。
- 结尾建议：换新模型正是大扫除时机——直接让 Astra 按本文标准审计你的指令库，然后去做以前不敢做的项目。

## 对 jackie_skills 的应用（2026-09-19 落地）

| 指南要点 | 应用位置 |
|---|---|
| description 短而精确 | `md/SKILL.md` frontmatter 重写（保留显式触发短语防漏触发） |
| 根文档最小路由 + 渐进披露 | `md/SKILL.md` 170→54 行；首建流程、钩子拆到 `md/references/`；脚本资产化 `md/scripts/pre-push`；`jiepi/SKILL.md` 瘦身为 WORKFLOW.md 的入口 |
| 软化菜谱、保留原则 | 同步流程步骤压缩为「盘点→矩阵→编辑」三段 + 原则式表述 |
| 定义完成防早停 | `md/SKILL.md`「完成定义」替代催促式自检清单 |
| 边界措辞信任式 | init-flow.md 要求生成的 AGENTS.md 不积强硬请示条款；真实安全偏好（不自动 commit、装钩子须同意）保留 |
| 文档按需指路 | init-flow.md「常驻最小集 + docs/agents/ 子文档」分层模板（阈值触发，小项目不分层） |
