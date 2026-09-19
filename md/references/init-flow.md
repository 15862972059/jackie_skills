# /md 首建流程 — 新项目生成 AGENTS.md + CLAUDE.md

目标产物：`AGENTS.md`（面向所有 AI 工具的单一信息源正文）+ `CLAUDE.md`（仅 `@AGENTS.md` 引用，可附 Claude 专属内容）。

1. **生成基础内容（扫描代码库）。**
   - **Claude Code**：用 Skill 工具调用内置 `init`，借它的扫描能力生成一份 `CLAUDE.md` 草稿。
   - **opencode / 其它无内置 init 的工具**：自己扫描代码库（README、依赖清单、目录结构、入口文件、路由/模型定义等）整理出项目概况，直接落到 `AGENTS.md`。
2. **按「常驻最小集 + 按需子文档」分层：**
   - `AGENTS.md` 只放**每次会话都需要**的内容：项目概述 / 技术栈 / 命令 / 项目结构 / 开发注意事项（硬约束与完成标准）——**只写项目真有的**，目标 ≤80 行。
   - 长主题放子文档，约定目录 `docs/agents/`，按扫描结果**只建真存在的**：`architecture.md`（服务边界、数据流）/ `database.md`（表、模型、迁移）/ `api.md`（端点清单）/ `workflow.md`（核心业务流程）/ `deployment.md`（部署与环境变量）。
   - **拆分阈值（不达标就不分层）**：单主题正文 >40 行、或全量正文预计 >120 行才拆子文档；小项目一份 `AGENTS.md` 装得下就保持单文件，**绝不为套模板而拆**。
   - 每个子文档在 `AGENTS.md` 对应位置只留一行按需指路：「改 schema / 迁移时读 `docs/agents/database.md`」。
   - 在 `AGENTS.md` 顶部加 HTML 注释：说明它是面向所有 AI 工具的单一信息源、更新走 `/md`、勿对它运行 `/init`（若工具有该命令）。
   - 把 `CLAUDE.md` 改写为：顶部一段「⚠️ 正文在 AGENTS.md，勿 /init 本文件」注释 + 一行 `@AGENTS.md`（其余 Claude 专属内容如有再附后）。
3. **AGENTS.md 写法要求（新模型时代，防指令腐化）：**
   - **文档按需指路**：写「改服务边界时看 architecture.md、改 schema 时看 database.md、准备部署时看 deployment.md」；**绝不**写「每次编辑前读完全部文档」——那会烧上下文、拖慢每一次任务。
   - 边界条款用信任式：不要为旧模型积累一堆强硬「必须先询问」的条款，强模型会太当真而在你其实允许继续的地方停下。真实的安全边界（如「不自动 commit」）保留原样。
   - 对确认安全的流程可**主动授权**，例如：「本地测试用一次性 fixture、无生产访问。直接跑、修到通过、重跑受影响用例，无需每步请示。」
   - 常用任务写清**完成标准**（做到什么程度算完），别靠模型猜。
   - 每条指令定期自问「还需要吗」；宁少勿多。
4. **跨工具说明**：`@AGENTS.md` 是 Claude Code 应用层 import（Win/Mac/Linux 一致）；opencode 原生读 `AGENTS.md`（也读 `.claude/skills`、`.agents/skills`）；**Codex** 读 `.agents/skills` 和 `AGENTS.md`；Antigravity 等其它 AI 编码工具直接读 `AGENTS.md` 正文。一份正文，各工具各自入口。
5. 首建完成后，可顺带提一句「还有一个可选的 pre-push 文档自动同步钩子」；用户有兴趣再读 [docsync-hook.md](docsync-hook.md) 展开。**写入任何钩子文件前需用户同意。**
