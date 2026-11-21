---
name: td-watch-and-ask
description: When you need to use todou cli to watch a issue or ask user questions.
---

## 后台监听卡片

Keep one running in the background, for listening the user's answer, comments, approvals or follow-ups:

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

- All text fields are markdown; validation is **strict** — unknown/extra fields fail with the path named.
- Users answer once, all questions in the comment together; "decline to answer" is a built-in exclusive
  choice; options and free-text "other" can coexist. Answers arrive decoded in the wait output.
- Exit codes follow the watch convention (0 answered / 3 timeout — re-wait / 1 error).
- One comment may carry 1–4 tightly related questions, each with your recommendation and reasoning.

## Waiting: watch, question wait, spec wait

有后台监听的时候可以不用这些命令。

```bash
todou agent can-i-follow                                               # ask first, then do what it prints
todou issue watch 16 -p <proj> --since <cursor> --forever              # one issue
todou question wait 16 <commentId> -p <proj> --forever                 # answers to one question comment
todou spec push 16 <dir> -p <proj> --message "v2" --wait               # push, then wait for the verdict
todou spec wait 16 -p <proj> [--since <cursor>]                        # re-enter that wait
```
