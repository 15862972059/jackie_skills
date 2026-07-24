---
name: readme
description: 参考 GitHub 风格为当前项目编写 README.md（中文）。扫描代码库真实信息后生成：徽章、标题+Slogan、简介、功能特性、技术栈、项目结构、安装、使用、配置/环境变量、开发、贡献、许可证等章节（只保留项目真实有的）。新项目首建或想重写/整理 README 时用。Works in Claude Code, opencode, and Codex. Triggers when the user types /readme or asks to write/generate/refresh the project README in Chinese.
compatibility: Claude Code, opencode, Codex
---

# /readme — 生成 GitHub 风格中文 README

你是项目 README 的**作者，不是模板填充机**。README 要读起来像「一个路过 GitHub 的人 30 秒内决定要不要用这个项目」的答卷。以真实代码为准，不臆造；中文撰写，参考 GitHub 主流开源项目的排版风格。本 skill 与具体项目、具体 AI 工具无关（Claude Code / opencode / Codex 均可用），在当前仓库自适应。

## 第 0 步：判断走哪条路

在仓库根目录检查现有 README，据此分流：

```bash
ls README.md readme.md README.zh.md 2>/dev/null; wc -l README* 2>/dev/null
```

- **A. 首建**：仓库没有 `README.md`（或只有空壳/自动生成的 stub）→ 走【首建流程】。
- **B. 重写/整理**：已有 `README.md` 且内容有规模 → 走【同步流程】（增量修正而非全量重建，避免丢失人工打磨的内容与历史结构）。

> Windows 文件名大小写不敏感，`README.md` 与 `readme.md` 同一文件，勿重复创建。

---

## 【首建流程】(A)

目标产物：仓库根目录 `README.md`（中文，GitHub 风格）。

### 1. 扫描代码库真实信息（不臆造）

按需用以下手段收集事实，能读到的才写进 README：

```bash
git remote -v                 # 仓库地址 → 安装/徽章链接
cat package.json pyproject.toml go.mod Cargo.toml pom.xml composer.json Gemfile 2>/dev/null  # 技术栈、依赖、脚本命令
ls -1                         # 项目结构
git log --oneline -5          # 项目活跃度（必要时）
```

- 若配了代码索引（如 Claude Code 的 CodeGraph `codegraph_*`）就用它核实项目结构、入口、核心模块；否则用 `ls`/`Read`。
- 拿不准的「语言/版本/命令/特性」，**读真实文件确认**，实在拿不到就留 `TODO` 占位并在报告里点出，绝不编造。

### 2. 章节选取（只保留项目真有的）

按 GitHub 主流 README 模板，**按需裁剪**（没用的章节直接删，不强行凑）：

1. **徽章行**（顶部，一行紧凑）：CI 状态、License、语言占比、版本、stars/last-commit 任选合适的。徽章用 shields.io 风格 `https://img.shields.io/...`，链接用真实仓库地址。**不要**堆砌无关徽章。
2. **项目名 + Slogan**：`# 项目名` + 一句 > 引用的短描述，点透「这是干嘛的」。
3. **语言切换**：若项目已有英文 README 需要双语，加 `[English](./README.md) · [中文](./README.zh.md)`；纯中文项目可省。
4. **简介/License 一句话说明**：一两句说清定位与 License。
5. **功能特性**：bullet 列表，每条一句话，写「能力」不写「实现细节」。
6. **技术栈**：badge 或表格列主要技术与版本。
7. **项目结构**：树形注释（只列关键目录，注释说明用途）。
8. **快速开始 / 安装**：可复制粘贴的真实命令（`git clone` → 依赖安装 → 启动）。
9. **使用**：最小可用示例或命令调用。
10. **配置 / 环境变量**（若有）：表格列出变量名、说明、默认值。
11. **开发**（若有）：`dev`/`test`/`build`/`lint` 命令。
12. **贡献**（开源项目才写）：简短的 PR/Issue 约定。
13. **许可证**：链接到 `LICENSE` 文件，明确 License 名。

### 3. 写作规范（GitHub 风格 + 中文）

- **中文撰写**：正文用中文；命令、代码、标识符、专有名词（如 `Docker`、`FastAPI`、`Vue3`）保留英文原词，前后用空格隔开中英文（如 "使用 FastAPI 构建"）。
- **排版**：善用 `---` 分隔大段；表格对齐；代码块标注语言；徽章行靠左、紧凑不换行刷屏。
- **命令可执行**：安装/使用代码块里的命令必须是真实可跑的，版本号与包名以依赖清单为准。
- **去水分**：少形容词、多事实；"强大""优雅"之类一律删。每个特性要对得上真实代码。
- **绝对日期**：若需写日期（如最后更新），用北京时间 `YYYY-MM-DD`，不写相对时间。

### 4. 落盘

用 **Write** 写到仓库根目录 `README.md`（首建允许整文件写入；已有内容则走同步流程，禁止 Write 整文件）。

### 5. 报告

一段话说清：扫描了哪些来源、保留了哪些章节、哪些信息拿不准打了 `TODO`、有无徽章/双语建议留给用户定夺。**不自动 commit**，除非用户要求。

---

## 【同步流程】(B) — 重写/整理已有 README

原则与 `/md` 一致：**减 > 增，合并 > 追加，删除 > 保留，修正 > 并存。** 同步后应更准、未必更长。

### 1. 对照真实代码，找出 README 里的过期/错误

```bash
git status --short
git diff HEAD --stat
git log --oneline -15
```

- 已提交则比对上次 README 以来：`git diff <last-readme-commit>..HEAD`。
- 重点核对：项目结构是否新增/删了顶层目录、技术栈版本是否换了、安装/使用命令是否还跑得通、环境变量/配置是否变化、License 是否变更。
- 用 CodeGraph 或 grep 核实，不靠记忆。

### 2. 影响 → 章节 矩阵

| 改了 | 检查的章节 |
|------|------|
| 依赖清单 / 技术栈版本 | 技术栈、安装命令、徽章里的版本 |
| 新增/删除顶层目录 | 项目结构 |
| 入口/启动方式变 | 快速开始、使用 |
| 配置/env 变化 | 配置 / 环境变量 |
| 改 License | 许可证、徽章 |
| 改仓库地址/CI | 徽章行链接 |
| 纯样式/文案/bugfix | 一般无需更新，告知后停止 |

### 3. 编辑（顺手清理）

用 **Edit** 精确替换逐段改，**不要 Write 整文件**。每段顺带：① 以真实代码为准修本次涉及章节；② 扫掉相关的过期/矛盾（换了的版本、删了的目录、失效的命令）；③ 合并重复。

### 4. 自检

- [ ] 保留的章节都还成立？ [ ] 命令/版本/结构对得上真实代码？ [ ] 徽章链接指向真实地址？
- [ ] 无夸大形容词、无水分？ [ ] 中英文之间有空格？ [ ] 只动该动的、没破坏人工打磨的结构？

### 5. 报告

一段话说清改了哪几段、为什么、顺手清了什么、有哪些留给用户定夺。**不自动 commit**，除非用户要求。

---

## 注意

- 本 skill 只写仓库根目录 `README.md`；项目内的子模块 README 不在范围内。
- 徽章：若仓库未公开/无 CI，宁可不加该徽章，不要放一个永远红色的假徽章。
- 双语：若用户没有要求多语言，默认纯中文 README；项目同时有英文读者时再提议加 `[English]·[中文]` 切换。
- 安装位置（`SKILL.md` 格式跨工具通用）：
  - Claude Code：`~/.claude/skills/readme/`（或项目 `.claude/skills/readme/`）
  - opencode：同上，也可放 `~/.config/opencode/skills/readme/`
  - Codex：`~/.agents/skills/readme/`（或项目 `.agents/skills/readme/`）