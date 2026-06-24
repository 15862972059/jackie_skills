---
name: md
description: 项目文档单一信息源的初始化与同步。新项目首次执行会调内置 /init 引导生成 AGENTS.md(正文)+CLAUDE.md(@引用);已有文档则以编辑者视角增量同步——合并重复、删过期、修矛盾;并可选装项目级 pre-push 钩子实现提交前自动同步。改完代码想同步/整理文档、或新项目想建文档时用。Triggers when the user types /md or asks to init/update/sync/tidy project docs.
---

# /md — 项目文档的初始化与同步

你是项目文档的**编辑者，不是日志记录员**。文档要读起来像「此刻、一个新接手的人需要知道的真相」。本 skill 与具体项目无关，在当前仓库自适应。

## 第 0 步：判断走哪条路

在仓库根目录检查文档现状，据此分流：

- **A. 首建**：既无 `AGENTS.md` 也无 `CLAUDE.md`（或 `CLAUDE.md` 是空壳/模板）→ 走【首建流程】。
- **B. 同步**：已有正文文件 → 走【同步流程】。

先确定**正文文件**（单一信息源，优先级）：
1. 有 `AGENTS.md` → 它是正文。
2. `CLAUDE.md` 仅含 `@AGENTS.md` 这类引用 → 正文是 `AGENTS.md`，**不要改 CLAUDE.md**。
3. 只有完整的 `CLAUDE.md` → 它是正文。

```bash
ls AGENTS.md agents.md CLAUDE.md 2>/dev/null; wc -l AGENTS.md CLAUDE.md 2>/dev/null
```

---

## 【首建流程】(A)

目标产物：`AGENTS.md`（面向所有 AI 工具的正文）+ `CLAUDE.md`（仅 `@AGENTS.md` 引用 + 可放 Claude 专属内容）。

1. **调内置 `/init` 生成基础内容。** 用 Skill 工具调用 `init`，它会扫描代码库生成一份 `CLAUDE.md`。这一步借它的项目扫描能力，省去从零摸索。
2. **重构成双文件单一信息源：**
   - 把 `/init` 生成的 `CLAUDE.md` 全部正文**移动**到 `AGENTS.md`（按通用结构组织：项目概述 / 技术栈 / 命令 / 项目结构 / 架构 / 核心业务流程 / 数据库 / API / 数据流 / 开发注意事项 / 部署，只保留项目真有的章节）。
   - 在 `AGENTS.md` 顶部加 HTML 注释：说明它是面向所有 AI 工具的单一信息源、更新走 `/md`、勿对它运行 `/init`。
   - 把 `CLAUDE.md` 改写为：顶部一段「⚠️ 正文在 AGENTS.md，勿 /init 本文件」注释 + 一行 `@AGENTS.md`（其余 Claude 专属内容如有再附后）。
3. **跨平台说明：** `@AGENTS.md` 是 Claude Code 应用层 import（Win/Mac/Linux 一致）；Codex/opencode/Antigravity 等直接读 `AGENTS.md` 正文，不走 CLAUDE.md。
4. 建好后 → 进入下方【可选：装 pre-push 钩子】询问。

---

## 【同步流程】(B)

原则：

- **文档是规则手册，不是 changelog。** 写「系统现在是什么样」，历史归 git log。
- **减 > 增，合并 > 追加，删除 > 保留，修正 > 并存。** 同步后应更准、未必更长。
- **判据：下一个接手的人需要知道吗？** 绝对日期。不臆造——拿不准就读真实代码确认。

### 1. 盘点改动

```bash
git status --short
git diff HEAD --stat
git diff HEAD
```
已提交则比对上次文档同步以来：`git log --oneline -15` + `git diff <last-doc-commit>..HEAD`。
配了 CodeGraph（`codegraph_*`）就用它核实哪些路由/模型/函数真变了。

### 2. 改动 → 章节 影响矩阵

| 改了 | 检查的章节 |
|------|------|
| 后端路由 / controller / API | API / Endpoints、数据流 |
| ORM 模型 / schema / migration | 数据库表、模型列表 |
| 业务逻辑 / service | 核心业务逻辑、业务流程、关键规则 |
| 配置 / env | 配置、环境变量 |
| 种子数据 / seed | 种子账号 / 初始数据 |
| 前端路由（pages.json / router） | 项目结构、路由、入口 |
| 新增页面 / 模块 | 项目结构、对应流程 |
| 依赖清单 | 技术栈、命令 |
| 新增顶层目录 | 项目结构树 |

仅样式/文案/bugfix → 告知「无需更新」并停止。

### 3. 编辑（顺手清理）

用 **Edit** 精确替换逐段改，**不要 Write 整文件**。每段顺带：① 以真实代码为准修本次涉及章节；② 扫掉相关的过期/矛盾（被重构的字段、改过的阈值、删掉的端点）；③ 合并重复。
谨慎删除：只删「已被代码证伪」或「明显重复」的；手写经验段除非被推翻否则保留；拿不准的保留并在报告里点出。

### 4. 自检

- [ ] 本次涉及章节都更新了？ [ ] 无 changelog 式叙述？ [ ] 顺手清了过期/矛盾/重复？
- [ ] 新增日期为绝对日期？ [ ] 只动正文文件、没碰引用型 CLAUDE.md？ [ ] 没无谓变长？

### 5. 报告

一段话说明改了哪几段、为什么、顺手清了什么、有哪些留给用户定夺。**不自动 commit**，除非用户要求。

---

## 【可选：装项目级 pre-push 钩子】

**仅在首建后、或用户主动要求时**，询问：「要不要装一个项目级 pre-push 钩子，push 前自动用 /md 逻辑同步文档？」

> 为什么是 pre-push 而非全局：项目级钩子只影响本仓库，绝不旁路其它 repo 的钩子（全局 `core.hooksPath` 会有此副作用）。

用户同意才装。安装步骤：

1. 写脚本到 `.git/hooks/pre-push`（本地、不入版本库）。若用户想让钩子随仓库分享，改写到 `.githooks/pre-push` 并 `git config --local core.hooksPath .githooks`。
2. 脚本内容（已泛化、自带 opt-in 门控与失败放行）：

```sh
#!/bin/sh
# pre-push: 检测结构性代码改动且本次未同步 AGENTS.md 时，调 claude -p 增量更新。
# 跳过本次: SKIP_DOCSYNC=1 git push
set -u
[ "${SKIP_DOCSYNC:-0}" = "1" ] && exit 0
repo_root=$(git rev-parse --show-toplevel 2>/dev/null) || exit 0
AGENTS=""; for f in "$repo_root/AGENTS.md" "$repo_root/agents.md"; do [ -f "$f" ] && AGENTS="$f" && break; done
[ -z "$AGENTS" ] && exit 0
range=""
while read -r l lsha r rsha; do
  case "$lsha" in *[!0]*) : ;; *) continue ;; esac
  z="0000000000000000000000000000000000000000"
  [ "$rsha" = "$z" ] && range="$lsha --not --remotes" || range="$rsha..$lsha"; break
done
[ -z "$range" ] && range="HEAD~1..HEAD"
changed=$(git diff --name-only $range 2>/dev/null); [ -z "$changed" ] && exit 0
printf '%s\n' "$changed" | grep -qiE '(^|/)agents\.md$' && exit 0
structural=$(printf '%s\n' "$changed" | grep -E \
  -e '\.(py|ts|tsx|js|jsx|vue|go|rs|java|kt|rb|php|cs|swift|c|cc|cpp|h|hpp|sql|proto)$' \
  -e '(^|/)(package\.json|pyproject\.toml|go\.mod|Cargo\.toml|pom\.xml|build\.gradle|composer\.json|Gemfile|requirements\.txt)$' \
  -e '(^|/)(pages\.json)$' -e '(^|/)migrations?/')
[ -z "$structural" ] && exit 0
echo "[docsync] 检测到结构性改动但未同步 AGENTS.md："; printf '  %s\n' $structural | head -20
CLAUDE_BIN=""
if command -v claude >/dev/null 2>&1; then CLAUDE_BIN="claude"
elif [ -n "${APPDATA:-}" ] && [ -f "$APPDATA/npm/claude.cmd" ]; then CLAUDE_BIN="$APPDATA/npm/claude.cmd"
elif [ -f "$HOME/AppData/Roaming/npm/claude.cmd" ]; then CLAUDE_BIN="$HOME/AppData/Roaming/npm/claude.cmd"; fi
[ -z "$CLAUDE_BIN" ] && { echo "[docsync] 未找到 claude CLI，跳过。手动 /md 后再 push（或 SKIP_DOCSYNC=1 git push）"; exit 0; }
echo "[docsync] 正在用 claude 增量更新 AGENTS.md……"
P="本仓库即将 push。请增量更新根目录 AGENTS.md（单一信息源），只改受影响章节，绝不重建，不碰 CLAUDE.md。改动文件：
$changed
先 git diff $range 看改了什么，再对照更新 AGENTS.md 实际存在的对应章节，拿不准的读真实代码确认。无需改动则不动文件。"
if "$CLAUDE_BIN" -p "$P" --allowedTools "Read,Edit,Bash,Grep,Glob" >/dev/null 2>&1; then
  if ! git -C "$repo_root" diff --quiet -- "$AGENTS" 2>/dev/null; then
    echo "[docsync] ✅ AGENTS.md 已更新，请 review 后提交再 push（或 SKIP_DOCSYNC=1 git push 直推）"; exit 1
  else echo "[docsync] 无需改动，放行。"; exit 0; fi
else echo "[docsync] claude 调用失败，跳过（不阻塞 push）。"; exit 0; fi
```

3. `chmod +x .git/hooks/pre-push`；`sh -n` 验证语法。
4. 告知用户：钩子已装、如何跳过（`SKIP_DOCSYNC=1 git push`）、如何卸载（删该文件）。

## 注意

- Windows 文件名大小写不敏感（`AGENTS.md` 与 `agents.md` 同一文件）。
- 钩子里的 `claude -p` 我无法在装配时实跑验证，需用户真实 push 才触发。
