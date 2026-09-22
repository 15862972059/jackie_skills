# jackie_skills

> A curated collection of modular skills for modern AI coding tools: **Antigravity**, **Codex**, **Claude Code**, and **opencode**.
> 一组面向现代 AI 编码工具（**Antigravity** / **Codex** / **Claude Code** / **opencode**）的模块化技能集合。

[English](#english) · [中文说明](#中文说明)

---

## English

### Overview

`jackie_skills` provides cross-tool, multi-agent capabilities following the standardized `SKILL.md` specification. No servers, no database, zero lock-in.

### Skills Catalog

| Skill | Trigger / Command | Core Function | Host Native Features |
| :--- | :--- | :--- | :--- |
| **`/md`** | `/md` | **Single source of truth doc sync**. Keeps `AGENTS.md` as the body and reduces `CLAUDE.md` to a one-line `@AGENTS.md` import, so every AI tool reads one document instead of two drifting copies. | Scaffold for new projects · impact-matrix incremental sync · optional pre-push hook |
| **`wsg-voice-synthesis`** | skill name | **Gallery voice-over synthesis SOP**. One command per exhibit-hall script, followed by 4 automatic acoustic quality gates (title peak pitch, room-tone RMS, pure-zero ratio, edge time-domain jump). | Local CPU inference, no API key required |
| **`wsg-voice-audit`** | skill name | **Full-corpus word-by-word voice audit**. Locates every occurrence of a tricky polyphonic character with millisecond timestamps, then ranks tone-contour anomalies so humans only listen to the few suspicious spots. | FunASR ONNX + Praat, CPU only |
| **`/jiepi`** | `/jiepi` or "design a page" | **Staged UI design-to-code workflow**. Category visual chooser (Mini-programs / PC Web) → brief & motion/effects collection → 3 concept mockups → approved code. | **Antigravity / Codex native image generation**, zero-port Artifact UI |

---

### Deep Dive: `/md`

**The problem it solves.** Claude Code reads `CLAUDE.md`, while Codex / opencode / Antigravity read `AGENTS.md`. Switching tools means maintaining the same memory twice — and as the code keeps iterating, both copies fall behind. `/md` makes one document authoritative and gets rid of the second one.

**Three modes**

| Mode | When | Output |
| :--- | :--- | :--- |
| **Scaffold** | New project, or both memory files are still empty shells / templates | Scans the codebase, writes `AGENTS.md` as the body (target ≤80 lines), reduces `CLAUDE.md` to a one-line `@AGENTS.md` import |
| **Incremental sync** | Body already exists and the code has changed | Touches only the sections this change affects, as a minimal diff |
| **Auto-sync (optional)** | You don't want to remember to run it | A pre-push hook that syncs before every commit — nothing is written without your consent |

**The four-step sync**

1. **Inventory the change** — `git status` / `git diff HEAD` / `git log`; verify against the host's code index when one is available.
2. **Impact matrix** — route / controller change → API section; schema / migration change → database section; new frontend route or top-level directory → project-structure section.
3. **Minimal-diff edit** — Edit the specific paragraphs. **Never rewrite the whole file**: a rewrite silently drops the experience sections you hand-wrote.
4. **Definition of done** — every affected section is either updated or explicitly justified as unaffected; stale, contradictory and duplicated lines get cleaned up along the way; only the body file is touched, never the reference-only `CLAUDE.md`.

**Editorial stance.** The document is a **rulebook, not a changelog** — it describes what the system *is*, while history belongs to `git log`. **Subtract > add, merge > append, delete > keep, correct > coexist.** After a sync it should be more accurate, not necessarily longer. The only test that matters: *does the next person to take over need to know this?*

**Written for strong models (anti-rot).** Instructions shift from *hard constraints for old models* to *trust-based statements for strong ones*.

| Legacy style | Modern style |
| :--- | :--- |
| "Read the entire document before every edit" | Point on demand: "see `architecture.md` for service boundaries, `database.md` for schema" — reading everything burns context and slows every task |
| A pile of "you must ask first" clauses | Trust-based boundaries: a strong model takes them literally and stops where you actually wanted it to continue |
| Only listing what is forbidden | Proactively authorise verified-safe flows: "local tests use throwaway fixtures — just run them, fix until green, no need to ask each step" |
| Letting the model guess when it is done | Write explicit **definitions of done** for common tasks |
| Documents that only ever grow | Ask of every instruction, periodically: "still needed?" — fewer is better |

**Layering discipline.** The always-loaded set (`AGENTS.md`) holds only what every session needs — target ≤80 lines. Split into `docs/agents/` sub-documents (architecture / database / api / workflow / deployment) only when a single topic exceeds ~40 lines or the whole body is heading past ~120 lines, leaving one line of on-demand pointer in the body. Small projects stay single-file — **never split just to fit a template**.

**One body, many entry points.** `@AGENTS.md` is a Claude Code application-layer import; opencode, Codex, Antigravity and other agents read the `AGENTS.md` body directly.

**Source.** The conventions above put into practice the ideas in OpenAI's developer blog post *Rethinking skills and prompts for GPT-6 Astra* (Sept 2026, Eric Provencher): when model capability jumps, the skill descriptions, `AGENTS.md` instructions and task prompts accumulated for older models all need to shed weight — less context, more trust, sharper definitions of done. Reading notes and a point-by-point mapping of how each idea landed in this repo: [`docs/guides/rethinking-skills-prompts-gpt6-astra.md`](docs/guides/rethinking-skills-prompts-gpt6-astra.md).

---

### Quick Installation (One-Click)

Clone the repository and run the setup script for your operating system:

```bash
git clone https://github.com/15862972059/jackie_skills.git
cd jackie_skills
```

- **Windows (PowerShell)**:
  ```powershell
  .\install.ps1
  ```
- **macOS / Linux**:
  ```bash
  chmod +x install.sh && ./install.sh
  ```

The installer automatically detects installed environments (`Claude Code`, `Codex`, `opencode`, and `Antigravity`), links or copies all skills into their global registries, and configures shortcuts.

### Manual Installation by Platform

<details>
<summary><b>1. Antigravity (Google AGY)</b></summary>

Copy skills to your global Gemini configuration or workspace `.agents` directory:
```bash
# Global
cp -r md jiepi wsg-voice-synthesis wsg-voice-audit ~/.gemini/config/skills/

# Or workspace-only
cp -r md jiepi wsg-voice-synthesis wsg-voice-audit <your-project>/.agents/skills/
```
In Antigravity, skills and workflows are automatically discovered. You can trigger `/jiepi` directly via natural language or typing `/jiepi`.
</details>

<details>
<summary><b>2. Claude Code</b></summary>

```bash
# User-level (all projects)
cp -r md jiepi wsg-voice-synthesis wsg-voice-audit ~/.claude/skills/

# Global command for /jiepi
cp install.sh ~/.claude/commands/ # or run install script
```
</details>

<details>
<summary><b>3. Codex</b></summary>

```bash
# User-level
cp -r md jiepi wsg-voice-synthesis wsg-voice-audit ~/.agents/skills/

# Project-level
cp -r md jiepi wsg-voice-synthesis wsg-voice-audit <your-project>/.agents/skills/
```
</details>

<details>
<summary><b>4. opencode</b></summary>

```bash
cp -r md jiepi wsg-voice-synthesis wsg-voice-audit ~/.config/opencode/skills/
```
</details>

---

## 中文说明

### 技能矩阵

| 技能 | 触发命令 | 核心定位 | 特性亮点 |
| :--- | :--- | :--- | :--- |
| **`/md`** | `/md` | **项目文档初始化与同步（单一真理源）** | 新项目首建 `AGENTS.md` 正文 + `CLAUDE.md` 一行 `@AGENTS.md` 引用；已有项目按影响矩阵增量同步，只做最小 diff（减 > 增）。可选 pre-push 提交前自动同步。 |
| **`wsg-voice-synthesis`** | 技能名 | **展厅语音合成 SOP（项目沉淀）** | 一条命令合成一段讲解词，跑完自动过 4 条声学质检门禁：标题峰值音高、停顿区底噪 RMS、纯数字零占比、时域边缘一阶跳变。依赖本机模型环境。 |
| **`wsg-voice-audit`** | 技能名 | **全量录音逐字核查（项目沉淀）** | 扫全部录音、逐字定位多音字并给出毫秒区间，再按声调异常排序，人只听最可疑的几处。FunASR 逐字时间戳 + Praat 基频估计。 |
| **`/jiepi`** | `/jiepi` 或“设计界面” | **前端 UI 洁癖工作流（设计到代码）** | 分类真实模板（微信小程序/PC网页）→ 收集需求与动效/特效偏好 → **宿主原生生图**生成 3 套概念图 → 用户确认后编码落地。 |

---

### `/md` 详解

**它解决什么**：Claude Code 读 `CLAUDE.md`，Codex / opencode / Antigravity 读 `AGENTS.md`，换个工具就得维护两份一模一样的记忆；而且代码一直在迭代，记忆文件很快跟不上步伐。`/md` 让**一份正文说话**，另一份退化成一行引用。

**三种运行模式**

| 模式 | 什么时候用 | 产物 |
| :--- | :--- | :--- |
| **首建** | 新项目，或两个记忆文件都还是空壳 / 模板 | 扫描代码库生成 `AGENTS.md` 正文（目标 ≤80 行），`CLAUDE.md` 精简为一行 `@AGENTS.md` |
| **增量同步** | 已有正文、代码改完要同步 | 按影响矩阵只更新受影响章节，最小 diff |
| **自动同步（可选）** | 不想每次手动记着 | pre-push 钩子，提交前自动同步一次——**写入任何钩子文件前都会先征得你同意** |

**同步四步**

1. **盘点改动** — `git status` / `git diff HEAD` / `git log`；宿主有代码索引就用它核实，没有就 grep、读文件确认。
2. **影响矩阵** — 改路由 / controller → 查 API 章节；改 schema / migration → 查数据库章节；改前端路由、加顶层目录 → 查项目结构章节。
3. **最小 diff 编辑** — 用 Edit 改动具体段落，**绝不整文件重写**：重写会连带丢掉你手写的经验段。
4. **完成定义** — 受影响章节要么更新、要么能说明为何不用更新；顺手清掉过期、矛盾、重复；只动正文文件，不碰引用型 `CLAUDE.md`。

**编辑立场**：文档是**规则手册，不是 changelog** —— 写「系统现在是什么样」，历史交给 `git log`。**减 > 增，合并 > 追加，删除 > 保留，修正 > 并存**；同步后应更准、未必更长。判据只有一条：**下一个接手的人需要知道吗？**

**为新模型写的（防指令腐化）**：文档从「给旧模型的强制约束」改成「给强模型的信任式说明」。

| 旧写法 | 新写法 |
| :--- | :--- |
| 「每次编辑前必须读完全部文档」 | 按需指路：「改服务边界看 `architecture.md`、改 schema 看 `database.md`」——否则烧上下文、拖慢每一次任务 |
| 堆一堆「必须先询问」的条款 | 信任式边界：强模型会太当真，在你其实允许继续的地方停下来 |
| 只写「不许做什么」 | 对确认安全的流程主动授权：「本地测试用一次性 fixture，直接跑、修到通过，无需每步请示」 |
| 让模型猜做到什么程度算完 | 常用任务写清**完成标准** |
| 文档只增不减 | 每条指令定期自问「还需要吗」——宁少勿多 |

**分层纪律**：常驻最小集（`AGENTS.md`）只放每次会话都需要的内容，目标 ≤80 行；只有单主题超 ~40 行、或全量正文预计破 ~120 行，才拆出 `docs/agents/` 子文档（architecture / database / api / workflow / deployment），正文对应位置只留一行按需指路。小项目一份装得下就保持单文件，**绝不为套模板而拆**。

**一份正文，各工具各自入口**：`@AGENTS.md` 是 Claude Code 的应用层 import；opencode、Codex、Antigravity 等直接读 `AGENTS.md` 正文。

**出处**：上面这套写法落地自 OpenAI 开发者博客《Rethinking skills and prompts for GPT-6 Astra》（2026-09，Eric Provencher）——模型能力跃升后，为旧模型堆起来的技能描述、`AGENTS.md` 指令与提示词需要同时「减负」：上下文越省越好、措辞越信任越好、完成标准越明确越好。原文要点笔记与逐条落地对照见 [`docs/guides/rethinking-skills-prompts-gpt6-astra.md`](docs/guides/rethinking-skills-prompts-gpt6-astra.md)。

---

### 一键安装（推荐）

克隆本仓库并在终端运行针对你操作系统的安装脚本：

```bash
git clone https://github.com/15862972059/jackie_skills.git
cd jackie_skills
```

- **Windows (PowerShell)**：
  ```powershell
  .\install.ps1
  ```
- **macOS / Linux**：
  ```bash
  chmod +x install.sh && ./install.sh
  ```

安装脚本会自动探测系统中的 `Claude Code`、`Codex`、`opencode` 和 `Antigravity`，一键将全部技能和全局斜杠命令配置到对应目录。

### 各工具手动配置指引

<details>
<summary><b>1. Antigravity (Google AGY)</b></summary>

复制到全局配置目录即可全局感知：
```bash
cp -r md jiepi wsg-voice-synthesis wsg-voice-audit ~/.gemini/config/skills/
```
在 Antigravity 中，启动 `/jiepi` 支持右侧原生 Artifact 面板交互，零端口依赖，并直接调用宿主内置工具 `generate_image` 生图。
</details>

<details>
<summary><b>2. Claude Code</b></summary>

```bash
cp -r md jiepi wsg-voice-synthesis wsg-voice-audit ~/.claude/skills/
```
在终端内直接输入 `/md` 或 `/jiepi` 即可使用；`wsg-voice-*` 两个技能不绑定斜杠命令，用自然语言唤起。
</details>

<details>
<summary><b>3. Codex</b></summary>

```bash
cp -r md jiepi wsg-voice-synthesis wsg-voice-audit ~/.agents/skills/
```
原生支持 `image_generate` 工具生图，输入 `/jiepi` 或自然语言唤起。
</details>

<details>
<summary><b>4. opencode</b></summary>

```bash
cp -r md jiepi wsg-voice-synthesis wsg-voice-audit ~/.config/opencode/skills/
```
</details>

---

## 许可证

[MIT](./LICENSE)
