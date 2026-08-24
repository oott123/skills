---
name: td-impl
description: 使用 todou 实现计划
---

# Todou impl

这份技能旨在帮助 Agent 了解如何结合 todou 实现计划。

## 关于 todou

Todou 是我们的任务追踪平台。

你应当可以使用 `todou` CLI 与其交互。该 CLI 应该已经登录并配置好项目绑定，如果没有，请指示用户手动登录或绑定，而不是自行探索。

通常你可以直接使用文本模式与 todou cli 交互。如果你偶尔需要结构化数据输出，请将 `--json` 加到 `todou` 之后。

## 读取 issue 标题和 spec 状态

本技能的参数应该是一个 issue 卡片/链接。 ISSUE_ID 是 issue 编号。

```bash
export ISSUE_ID=1 # or #2, T-3, ISSUE-4 -- they are valid formats too
todou issue view $ISSUE_ID --json | jq -r '.issue.title'
todou spec status $ISSUE_ID
```

读取标题有助于了解该 issue 的主旨。检查 spec 最后一个版本是否为 approved - 如果不是，则停止工作，并要求用户先批准计划。

如果有 spec 就不需要完整阅读 issue 了，但如果完全没有 spec，请阅读 issue 本体：

```bash
todou issue view $ISSUE_ID
```

并了解 issue 主题或者最新的评论中是否含有明确的任务，例如 bug 修复或者简单的功能实现。如果有，则直接按指示工作。否则停下来向用户汇报无法继续实现。

## 开始工作

创建一个 scratch 目录，并使用 todou cli 拉取 specs、将卡片置于 "In Progress" 状态。

```bash
SCRATCH_DIR=/tmp/scratch-name && mkdir $SCRATCH_DIR && todou spec pull $ISSUE_ID $SCRATCH_DIR && tail -n +1 $SCRATCH_DIR/*.md
todou issue edit $ISSUE_ID --status "In Progress"
```

Please attempt to read the following files directly from the specifications directory without checking if they exist (simply ignore any non-existent files):

- `proposal.md` - This contains the user's original requirements
- `design.md` - This is the high-level design for this task
- `api.md` - This is the API design involved in this task
- `plan.md` - This is the specific execution plan

Once read, please execute your plan according to `plan.md`. If the plan consists of multiple steps, please create a task list and use it to manage your execution progress.

If the user provides additional information during the execution process, or if you obtain any supplementary details or extra context by asking the user, you must update the `proposal.md` file accordingly. If the user makes corrections to the proposal, directly modify the relevant sections in `proposal.md` instead of appending new, conflicting, or indecisive entries.

## 完成工作

将改动提交。如果当前在 master 上，那么就提交到 master，而不是新建分支。如果当前是一个在 worktree 上的 subagent，提交后让主 agent 合回 master。

将卡片置于 "Ready to Ship" 状态：

```bash
todou issue edit $ISSUE_ID --status "Ready to Ship"
```

你也可以同时留下一些简短的评论以介绍工作内容：

```bash
todou comment add $ISSUE_ID --body-file <(cat <<'EOF'
comments here...
EOF
)
```

如果修改了 specs，记得提交 specs 文件：

```bash
todou spec push $ISSUE_ID $SCRATCH_IDR --message "v2"
```

## 请求澄清或修改计划

如果在工作过程中，需要向用户询问问题或请求澄清，需要使用 todou CLI 将问题提交到平台上，并等待用户回复。

Do not use AskUserQuestion or any built-in tools:

```bash
todou comment add $ISSUE_ID --body-file <(cat <<'EOF'
question context here...
EOF
) --questions <(cat <<'EOF'
[{"header": "Storage", "question": "Where should X live?", "options": [{"label": "Reuse mechanism A", "description": "pros/cons…"}, {"label": "New entity"}], "multiple": false}]
EOF
)
```

提交完成后，使用一个后台命令，以等待用户回复：

```bash
todou question wait $ISSUE_ID $COMMENT_ID --timeout 3000
```

- All text fields are markdown; validation is **strict** — unknown/extra fields fail with the path named.
- Users answer once, all questions in the comment together; "decline to answer" is a built-in exclusive
  choice; options and free-text "other" can coexist. Answers arrive decoded in the wait output.
- Exit codes follow the watch convention (0 answered / 3 timeout — re-wait / 1 error).
- One comment may carry 1–4 tightly related questions, each with your recommendation and reasoning.

如果用户在时限内没有回复，则停止工作。
