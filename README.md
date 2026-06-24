# jackie_skills

> A collection of [Claude Code](https://claude.ai/code) / [opencode](https://opencode.ai) skills.
> 一组 [Claude Code](https://claude.ai/code) / [opencode](https://opencode.ai) 技能集合。

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

### Install

Works in both **Claude Code** and **opencode** (opencode natively discovers `~/.claude/skills/`).

```bash
git clone https://github.com/15862972059/jackie_skills.git

# Claude Code — user-level (all projects)
cp -r jackie_skills/md ~/.claude/skills/md

# opencode — also reads ~/.claude/skills, or use its own dir:
cp -r jackie_skills/md ~/.config/opencode/skills/md
```

Or for a single project (both tools read `.claude/skills/`):

```bash
cp -r jackie_skills/md <your-project>/.claude/skills/md
```

Then run `/md` in Claude Code or opencode.

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

### 安装

**Claude Code 和 opencode 均可用**（opencode 原生识别 `~/.claude/skills/`）。

```bash
git clone https://github.com/15862972059/jackie_skills.git

# Claude Code — 用户级（所有项目生效）
cp -r jackie_skills/md ~/.claude/skills/md

# opencode — 同样读 ~/.claude/skills，或放到它自己的目录：
cp -r jackie_skills/md ~/.config/opencode/skills/md
```

或仅装到单个项目（两个工具都读 `.claude/skills/`）：

```bash
cp -r jackie_skills/md <你的项目>/.claude/skills/md
```

然后在 Claude Code 或 opencode 里运行 `/md`。

---

## License

[MIT](./LICENSE)
