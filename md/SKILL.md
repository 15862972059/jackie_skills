---
name: md
description: 项目文档单一信息源：以编辑者视角增量同步 AGENTS.md（合并重复、删过期、修矛盾），或为新项目首建 AGENTS.md + CLAUDE.md（@引用）。用户输入 /md，或说"改完代码同步/整理项目文档""新项目写 AGENTS.md"时用。可选 pre-push 提交前自动同步。首建与钩子细节按需读 references/。
compatibility: Claude Code, opencode, Codex
---

# /md — 项目文档的初始化与同步

你是项目文档的**编辑者，不是日志记录员**。文档要读起来像「此刻、一个新接手的人需要知道的真相」。本 skill 与具体项目、与具体 AI 工具无关，在当前仓库自适应。

**路径基准**：本文中的相对路径以本技能目录（SKILL.md 所在目录）为基准；技能目录必须整目录安装（references/ 与 scripts/ 随 install.sh / install.ps1 一并复制）。

## 第 0 步：分流

检查仓库根目录的文档现状（注意 Windows 大小写不敏感，`AGENTS.md` 与 `agents.md` 是同一文件）：

- 既无正文型 `AGENTS.md`、`CLAUDE.md` 也是空壳/模板 → **首建**：读 [references/init-flow.md](references/init-flow.md) 照做。
- 已有正文 → 走下方**同步流程**。
- 用户问到「提交前自动同步 / pre-push 钩子」→ 读 [references/docsync-hook.md](references/docsync-hook.md)。

正文文件（单一信息源）判定优先序：有 `AGENTS.md` 即它是正文；`CLAUDE.md` 仅含 `@AGENTS.md` 引用时正文仍是 `AGENTS.md`，**不要改 CLAUDE.md**；只有完整 `CLAUDE.md` 时它是正文。

## 【同步流程】

原则：

- **文档是规则手册，不是 changelog。** 写「系统现在是什么样」，历史归 git log。
- **减 > 增，合并 > 追加，删除 > 保留，修正 > 并存。** 同步后应更准、未必更长。
- 判据只有一条：**下一个接手的人需要知道吗？** 日期用绝对日期；不臆造——拿不准就读真实代码确认。

1. **盘点改动**：`git status --short` + `git diff HEAD --stat` + `git diff HEAD`；已提交则 `git log --oneline -15` 并对上次文档同步点做 diff。宿主配了代码索引就用它核实，否则 grep/读文件确认。
2. **影响矩阵**（改了什么 → 检查哪些章节；章节在 `AGENTS.md` 或它指路的 `docs/agents/` 子文档里）：

| 改了 | 检查的章节 |
|------|------|
| 路由 / controller / API | API / Endpoints、数据流 |
| ORM 模型 / schema / migration | 数据库表、模型列表 |
| 业务逻辑 / service / 配置 env | 核心业务流程、关键规则、配置与环境变量 |
| 前端路由 / 新增页面 / 新增顶层目录 | 项目结构、路由与入口 |
| 依赖清单 | 技术栈、命令 |

仅样式/文案/纯 bugfix → 告知「无需更新」并给出判断依据，停止。

3. **编辑**：用 Edit 做最小 diff，不整文件重写（重写会连带丢掉用户手写经验段）。顺手修本次涉及章节的过期事实与矛盾、合并重复。删除谨慎：只删被代码证伪或明显重复的；手写经验段除非被推翻否则保留；拿不准的保留并在报告里点出。分层纪律：单章节将超约 40 行 → 拆入对应 `docs/agents/` 子文档，正文只留一行按需指路；反之不把子文档内容无故吸回正文。

## 完成定义（以下全部满足才算同步完成）

- 本次改动涉及的每个章节都已核对：更新过，或能说明为何不用更新；
- 相关的过期、矛盾、重复已顺手清理；
- 只动了正文文件，没碰引用型 `CLAUDE.md`；
- 文档没有无谓变长，新增日期均为绝对日期；
- 已向用户一段话报告：改了哪几段、为什么、清了什么、留什么给用户定夺。

**不自动 commit**，除非用户要求。
