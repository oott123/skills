---
name: implement-file-based-plan
description: Read this skill when the user requests the Agent to execute a spec.
---

# Execute a File-Based Plan

First, please note: `specs/<spec-name>` is the specifications directory, and the `proposal.md` file contains the user's original requirements.

Please attempt to read the following files directly from the specifications directory without checking if they exist (simply ignore any non-existent files):

- `proposal.md` - This contains the user's original requirements
- `design.md` - This is the high-level design for this task
- `api.md` - This is the API design involved in this task
- `plan.md` - This is the specific execution plan

Once read, please execute your plan according to `plan.md`. If the plan consists of multiple steps, please create a task list and use it to manage your execution progress.

If the user provides additional information during the execution process, or if you obtain any supplementary details or extra context by asking the user, you must update the `proposal.md` file accordingly. If the user makes corrections to the proposal, directly modify the relevant sections in `proposal.md` instead of appending new, conflicting, or indecisive entries.
