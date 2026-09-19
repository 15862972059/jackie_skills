# jackie_skills

> A collection of [Claude Code](https://claude.ai/code) / [opencode](https://opencode.ai) / Codex skills.
> 一组 [Claude Code](https://claude.ai/code) / [opencode](https://opencode.ai) / Codex 技能集合。

[English](#english) · [中文](#中文)

---

## English

### Skills

#### `/md` — Project doc init & sync

Keeps a project's **single source of truth** documentation in sync with the code, across multiple AI coding tools (Claude Code / Codex / opencode / Antigravity …).

The idea: most AI tools look for an `AGENTS.md`, while Claude Code looks for `CLAUDE.md`. Maintaining both by hand leads to drift. This skill keeps **`AGENTS.md` as the single body of truth** and reduces `CLAUDE.md` to a one-line `@AGENTS.md` import — so every tool reads the same content, and you only ever edit one file.

**What it does:**

- **First run on a new project** → calls the built-in `/init` to scan the codebase, then restructures the output into `AGENTS.md` (body) + `CLAUDE.md` (`@AGENTS.md` reference).
- **On an existing project** → incrementally syncs docs from `git diff` as an *editor, not a logger*: updates affected sections, merges duplicates, removes stale info, fixes contradictions. Never rebuilds the whole file.
- **Optionally** installs a project-level `pre-push` git hook that auto-syncs docs before push (opt-in, project-scoped — never touches other repos). The hook auto-detects `claude` or `opencode` CLI (override with `DOCSYNC_CLI`).

**Design principles:**

- Docs are a *rulebook*, not a changelog — describe what the system *is now*; history belongs in `git log`.
- Reduce > add, merge > append, delete > keep, fix > coexist.
- Never fabricate — when unsure about a rule or field, read the real code to confirm.

#### `/md` Design Notes · [中文](#md-设计手记)

**The problems** we kept hitting as AI coding tools accumulate instructions:

- **Doc drift** — parallel copies of project docs diverge, and stale rules keep steering agents invisibly.
- **Context bloat** — "read all docs before every edit" style mandates burn context on every task; oversized skill descriptions get truncated by hosts, and vague triggers load irrelevant skills.
- **Over-steering** — itinerary-style micro-steps and hard "always ask first" clauses, written for weaker models, now constrain stronger ones into stopping earlier than intended.

**How this project solves them:**

- Single source of truth + *editor, not logger* incremental sync: merge duplicates, delete stale, fix contradictions; docs must not grow without reason.
- Progressive disclosure **in the skill itself**: `md/SKILL.md` is a ~50-line router; the first-run flow and hook docs live in `md/references/`, and the `pre-push` hook ships as a ready script (`md/scripts/pre-push`) instead of being retyped from prose — the model only reads what the current path needs.
- Progressive disclosure **in what it generates**: the produced `AGENTS.md` keeps a minimal always-needed core (target ≤ 80 lines); long topics (architecture / database / API / workflow / deployment) split into `docs/agents/*.md` **only past a size threshold**, each leaving one "read X when doing Y" pointer behind. Small projects stay single-file.
- A written **definition of done** replaces nag checklists, so a sync ends neither early nor endless.

**The thinking** in one line: apply to the docs themselves what current agent-guidance recommends — shortest precise triggers, context on demand, trust-style boundaries, completion defined upfront. Full source notes: [docs/guides/rethinking-skills-prompts-gpt6-astra.md](docs/guides/rethinking-skills-prompts-gpt6-astra.md) (summary of OpenAI's guide by Eric Provencher, 2026-09-11).

### Install

Works in **Claude Code**, **opencode**, and **Codex** (all share the same `SKILL.md` format).

```bash
git clone https://github.com/15862972059/jackie_skills.git

# Claude Code — user-level (all projects)
cp -r jackie_skills/md ~/.claude/skills/md

# opencode — also reads ~/.claude/skills, or use its own dir:
cp -r jackie_skills/md ~/.config/opencode/skills/md

# Codex — use .agents/skills dir:
cp -r jackie_skills/md ~/.agents/skills/md
```

Or for a single project (all tools read their respective paths):

```bash
# Claude Code / opencode
cp -r jackie_skills/md <your-project>/.claude/skills/md

# Codex
cp -r jackie_skills/md <your-project>/.agents/skills/md
```

Then run `/md` in your tool.

---

## 中文

### 技能列表

#### `/md` — 项目文档初始化与同步

让项目的**单一信息源**文档与代码保持同步，并能被多个 AI 编码工具（Claude Code / Codex / opencode / Antigravity……）共同读取。

背景：多数 AI 工具找 `AGENTS.md`，而 Claude Code 找 `CLAUDE.md`。两份手工维护必然漂移。本技能让 **`AGENTS.md` 作为唯一正文**，`CLAUDE.md` 精简成一行 `@AGENTS.md` 引用——所有工具读同一份内容，你永远只改一个文件。

**它做什么：**

- **新项目首次执行** → 调用内置 `/init` 扫描代码库，再把产物重构成 `AGENTS.md`（正文）+ `CLAUDE.md`（`@AGENTS.md` 引用）。
- **已有项目** → 以「编辑者而非记录员」的视角，根据 `git diff` 增量同步：更新受影响章节、合并重复、删除过期、修正矛盾，绝不重建整份文件。
- **可选** 安装项目级 `pre-push` git 钩子，push 前自动同步文档（按需启用、仅作用于本仓库，绝不影响其它 repo）。钩子自动探测 `claude` 或 `opencode` CLI（可用 `DOCSYNC_CLI` 强制指定）。

**设计原则：**

- 文档是**规则手册**而非 changelog——写「系统现在是什么样」，历史归 `git log`。
- 减 > 增，合并 > 追加，删除 > 保留，修正 > 并存。
- 不臆造——拿不准的规则/字段，读真实代码确认。

#### `/md` 设计手记 · [English](#md-design-notes--中文)

**当前的问题**（AI 编码工具的指令越积越多后普遍出现）：

- **文档漂移**——多份并行的项目文档各自演化互相矛盾，过期规则还在隐形地指挥 agent。
- **上下文膨胀**——「每次编辑前读完所有文档」式指令每次都烧上下文；技能描述过长会被宿主截断，触发词写得太宽会加载用不上的技能。
- **过度牵引**——为弱模型写的菜谱式步骤和强硬「必须先请示」条款，到了强模型身上反而束缚发挥、诱发早停。

**本项目怎么解决：**

- 单一信息源 +「编辑者而非记录员」增量同步：合并重复、删过期、修矛盾，文档不许无谓变长。
- **技能自身**渐进披露：`md/SKILL.md` 只做约 50 行路由；首建流程与钩子说明放 `md/references/`，`pre-push` 以现成脚本资产（`md/scripts/pre-push`）随包发布而不是让模型从文档里抄写——模型只读当下路径需要的内容。
- **产物**同样渐进披露：生成的 `AGENTS.md` 只保留每次会话都需要的最小集（目标 ≤80 行）；长主题（架构/数据库/API/业务流程/部署）**超过阈值才**拆到 `docs/agents/*.md`，正文留一行「改 X 时读 Y」按需指路；小项目保持单文件，绝不为套模板而拆。
- 用写明的**完成定义**替代催促式自检清单，同步既不早停也不无限蔓延。

**思路**一句话：把新一代 agent 指南对指令库的要求——最短精确触发、按需上下文、信任式边界、预先定义完成——反过来用在文档本身上。出处与要点笔记：[docs/guides/rethinking-skills-prompts-gpt6-astra.md](docs/guides/rethinking-skills-prompts-gpt6-astra.md)（OpenAI 开发者博客，作者 Eric Provencher，2026-09-11）。

### 安装

**Claude Code / opencode / Codex 均可用**（三工具共享同一份 `SKILL.md` 格式）。

```bash
git clone https://github.com/15862972059/jackie_skills.git

# Claude Code — 用户级（所有项目生效）
cp -r jackie_skills/md ~/.claude/skills/md

# opencode — 同样读 ~/.claude/skills，或放到它自己的目录：
cp -r jackie_skills/md ~/.config/opencode/skills/md

# Codex — 放到 .agents/skills 目录：
cp -r jackie_skills/md ~/.agents/skills/md
```

或仅装到单个项目（各工具读各自路径）：

```bash
# Claude Code / opencode
cp -r jackie_skills/md <你的项目>/.claude/skills/md

# Codex
cp -r jackie_skills/md <你的项目>/.agents/skills/md
```

然后在你的 AI 工具里运行 `/md`。

---

## License

[MIT](./LICENSE)
