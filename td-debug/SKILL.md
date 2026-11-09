---
name: td-debug
description: 使用 todou 排查 bug
---

# Todou Debug

基于 todou issue tracker 的结构化 debug 指南

## 关于 todou

Todou 是我们的任务追踪平台。

你应当可以使用 `todou` CLI 与其交互。该 CLI 应该已经登录并配置好项目绑定，如果没有，请指示用户手动登录或绑定，而不是自行探索。

通常你可以直接使用文本模式与 todou cli 交互。如果你偶尔需要结构化数据输出，请将 `--json` 加到 `todou` 之后。

### 后台监听卡片

Keep one running in the background, for listening the user's answer or comments or spec approval:

```bash
todou issue watch $ISSUE_ID --since <cursor> --debounce 60 --forever
```

Before you start it, run `todou agent can-i-follow` and follow the instructions there.

Run it without `--json`: the line format carries each comment's opening, which is what you act on.

- Run `todou agent can-i-follow` and do what it says. It prints the instructions for this harness.
- Wait from the cursor the write itself printed: `spec push` and `comment add` end with a `cursor:`
  line holding their own position, and a cursor taken afterwards can already be past the answer.
- A wait killed from outside (the harness stopping a background task) is your wake-up, not an error:
  restart it with the same cursor, every time.
- Resume from the closing `cursor:` line.
- `issue watch`, `todou watch` and `spec wait` skip entries from your own agent session rather than
  your whole account.

### 问问题

需要与用户澄清的问题，需要使用 todou CLI 将问题提交到平台上，并等待用户回复。

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

- All text fields are markdown; validation is **strict** — unknown/extra fields fail with the path named.
- Users answer once, all questions in the comment together; "decline to answer" is a built-in exclusive
  choice; options and free-text "other" can coexist. Answers arrive decoded in the wait output.
- Exit codes follow the watch convention (0 answered / 3 timeout — re-wait / 1 error).
- One comment may carry 1–4 tightly related questions, each with your recommendation and reasoning.

### 提交文档

当你完成你的调查文档，需要通过 todou CLI 提交。Documents are written in a scratch dir and pushed; **git never carries them**.

```bash
todou spec push $ISSUE_ID $SCRATCH_IDR --message "v2"
```

在提交前你也可以为 issue 添加一条简短地评论向用户说明重点：

```bash
todou comment add $ISSUE_ID --body-file <(cat <<'EOF'
comments here...
EOF
)
```

spec 和评论中的 Markdown 语法都支持 mermaid 渲染，可以使用 code block 中的 mermaid 以渲染线框图/流程图而不是 ascii art。

提交完成后，根据用户回复进行如下处理：

- **request-changes** (or annotations arrive):
  `todou spec comments $ISSUE_ID --unresolved` lists inline annotations with file + anchor.
  Apply them to the documents, sync requirement changes into proposal.md, then
  `todou spec resolve $ISSUE_ID <commentIds…>` for each addressed annotation and
  `todou spec push $ISSUE_ID $SCRATCH_DIR --if-version <v> --message "plan v2"`. Wait again. Repeat until approved.
- **approve** → proceed.

## 进行调查

当用户提供日志或问题需要调查时，首先定位报错的代码位置。如果可能有多处，则一并找到。

日志可能足以或者不足以暴露问题。如果足以暴露问题，则直接定位根本原因。如果不足以暴露问题，则尝试自行复现。

如果能复现，则在可能出问题的地方增加日志，直到问题得以暴露。只在有风险的且不常见的分支使用 warning/info 级；在热点路径或者普通、常见的路径使用 debug 或 trace 级。

如果不能复现，则试探性地增加一批日志，请用户尝试复现并查看复现后的日志或者请用户贴上复现后的日志。如果仍然找不到问题，则增加更多的日志。需要用户自己启动本地的测试实例时，给出以合适的日志层级启动的命令。

## 清理日志

在找到根本原因后，需要清理为调试临时增加的日志，但不要全部移除，还是需要保留适当的日志使得该问题能被迅速定位。需要保持常规情况和 info 层级下正常工作时不会输出大量日志。

## 撰写报告

如果找到了问题，在 scratch 目录撰写以下文件，并推送到 todou 上等待审查：

- `bug.md` - 问题表现、复现方法
- `root-cause.md` - 导致该问题的基本原因
- `fix-plan.md` - 简明的修复方案

如果重复数轮都没有找到问题，撰写放弃报告：

- `bug.md` - 问题表现、复现方法（如果找到了）
- `failed-investigations.md` - 本次会话中所有失败的尝试路线，以及为什么它们不是问题原因
