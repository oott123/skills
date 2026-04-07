---
name: implement-file-based-plan
description: 当用户要求 Agent 执行某个 spec 时读取该技能。
---

# 执行一个基于文件的计划

首先，请了解： `specs/<spec-name>` 是规格书目录，`proposal.md` 文件是用户的原始需求。

请直接尝试读取规格书目录下的以下文件，无需检查是否存在（不存在的文件忽略即可）：

* `proposal.md` - 这是用户的原始需求
* `design.md` - 这是这个任务的大体设计
* `api.md` - 这是这个任务涉及的 API 设计
* `plan.md` - 这是具体的执行计划

读取后，请按 `plan.md` 执行你的计划。如果 plan 中有多步，请创建任务列表并使用它管理你的执行进展。
