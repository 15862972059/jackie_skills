# /md 可选 — 项目级 pre-push 文档同步钩子

**何时读本文**：首建流程末尾用户表示有兴趣，或用户主动要求「提交/推送前自动同步文档」。用户同意后才写入文件。

> 为什么是项目级 pre-push 而非全局 `core.hooksPath`：项目级钩子只影响本仓库，绝不旁路其它 repo 的钩子。

脚本正文是本技能目录下的资产文件 `scripts/pre-push`（唯一真理源，勿在文档或对话里重复抄写脚本内容）。行为概述：检测到 push 范围内有结构性代码改动但 `AGENTS.md` 未同步时，调用 AI CLI（`claude` 或 `opencode`，可用 `DOCSYNC_CLI=claude|opencode` 强制指定）增量更新 `AGENTS.md`；有改动则 `exit 1` 拦下 push 让用户 review，调用失败或无需改动则放行，绝不卡死推送。

## 安装（Git Bash / sh 环境）

```bash
HOOK="$(git rev-parse --git-path hooks)/pre-push"     # worktree / submodule 下同样安全
mkdir -p "$(dirname "$HOOK")"
sed 's/\r$//' "<技能目录>/scripts/pre-push" > "$HOOK"  # 必须去 CRLF，否则行尾 \r 使 SHA 比对永不匹配、钩子静默失效
chmod +x "$HOOK"                                      # Windows 上无意义但无害；文件名必须恰为 pre-push（无扩展名）
sh -n "$HOOK" && echo "syntax OK"                     # Windows 请在 Git Bash 里执行
```

想让钩子随仓库分享给团队：改放 `.githooks/pre-push`（同样去 \r、可入库），再 `git config --local core.hooksPath .githooks`。

## 使用与卸载

- 单次跳过：`SKIP_DOCSYNC=1 git push`
- 卸载：删除该钩子文件（团队版另需还原 `core.hooksPath`）
- 钩子内的 AI CLI 调用无法在安装时实跑验证，需一次真实 `git push` 才触发；可先用一个无关紧要的分支试推。
