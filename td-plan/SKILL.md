---
name: td-plan
description: 使用 todou 做计划
---

# Todou plan

这份技能旨在帮助 Agent 了解如何结合 todou 做计划。

## 关于 todou

Todou 是我们的任务追踪平台。

你应当可以使用 `todou` CLI 与其交互。该 CLI 应该已经登录并配置好项目绑定，如果没有，请指示用户手动登录或绑定，而不是自行探索。

通常你可以直接使用文本模式与 todou cli 交互。如果你偶尔需要结构化数据输出，请将 `--json` 加到 `todou` 之后。

## 做计划

本技能的参数应该是一个 issue 卡片/链接。如果用户提供的是一段自然语言文本描述，请先创建一个 issue。

首先你应当读取 issue 卡片。使用 `todou issue view $ISSUE_ID` 命令读取 issue，其中 ISSUE_ID 是问题编号。

Then follows the instructions below to draft a plan. 创建这些文件时，使用和用户输入相同的语言。

### When you need to draft a plan

- You need to create an scratch directory 比如说 `/tmp/plan-name` and write the user's original requirements into a file named `proposal.md` in it.
- Explore the code and plan the system design. Use explore/scout/researcher sub agents if needed. Ask the user about anything unclear to clarify the design details, trade-offs, and context.
- Create a new `design.md` file in the scratch directory and briefly describe your system and architectural design.
- If you need to introduce third-party libraries or manually implement a well-known algorithm, please also explain this in `design.md`.
- If API design is needed, create a new `api.md` file in the scratch directory and list the relevant API designs.
- Save your detailed execution plan in the `plan.md` file under the scratch directory. Be sure not save it anywhere else.
- If the user provides additional information during the planning or execution process, or if you obtain any supplementary details or extra context by asking the user, you must update the `proposal.md` file accordingly. If the user makes corrections to the proposal, directly modify the relevant sections in `proposal.md` instead of appending new, conflicting, or indecisive entries. This can be considered as explicitly requested modifications to the user's original requirements so modifications is allowed.
- Present only the final approach and exclude all intermediate thought processes or alternative plans.
- Finally, check your design and plan to ensure that there are no waffling or indecisive wording in all the design, plan or api docs. If there any, edit and remove them.

### When making the system design

- **No unsolicited compatibility layers.** DO NOT introduce compatibility shims, fallbacks, deprecated-path branches, or any code whose purpose is to preserve old behavior — unless the user explicitly asks for it. Prefer clean replacements that update all call sites. If you genuinely believe a compatibility layer is unavoidable or strongly warranted, **stop and ask the user before writing it**.
- **Fail fast on input; do not be lenient.** DO NOT add forgiving normalization of user/API input — no silent trimming of whitespace, no case-folding, no coercing empty strings to defaults, no "did you mean" guessing, no accepting near-miss formats. Validate strictly and reject invalid input with a clear error. Only relax this when the user explicitly asks for it.

## 问问题

做计划时需要与用户澄清的问题，需要使用 todou CLI 将问题提交到平台上，并等待用户回复。

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

## 提交计划

当你完成你的计划，需要通过 todou CLI 提交。Documents are written in a scratch dir and pushed; **git never carries them**.

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

提交完成后，使用一个后台命令，以等待用户回复：

```bash
todou issue watch $ISSUE_ID --type spec_review --since "$(todou watch --poll --json | jq -r .next_cursor)" --timeout 3000
```

根据用户回复进行如下处理：

- **request-changes** (or annotations arrive):
  `todou spec comments $ISSUE_ID --unresolved` lists inline annotations with file + anchor.
  Apply them to the documents, sync requirement changes into proposal.md, then
  `todou spec resolve $ISSUE_ID <commentIds…>` for each addressed annotation and
  `todou spec push $ISSUE_ID $SCRATCH_DIR --if-version <v> --message "plan v2"`. Wait again. Repeat until approved.
- **approve** → proceed.

如果用户在时限内没有回复，则停止工作。

## 完成计划

当你的计划通过后，如果用户没有说立即执行，则不要执行。清理你创建的后台终端（如果有），将卡片置于 `Next` 状态，然后结束会话。

```bash
todou issue edit $ISSUE_ID --status "Next"
```
