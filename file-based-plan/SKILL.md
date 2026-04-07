---
name: file-based-plan
description: Use this skill when the user asks the Agent to read a proposal file under specs/ and **make a plan**; it is not needed when the user asks to execute.
---

# File-Based Planning

First, please understand: `specs/<spec-name>` is the specifications directory, and the `proposal.md` file contains the user's original requirements.
Unless explicitly requested, the user's original requirements should not be modified.

When you need to draft a plan:

- Explore the code and plan the system design. Ask the user about anything unclear to clarify the design details, trade-offs, and context.
- Create a new `design.md` file in the specifications directory and briefly describe your system and architectural design.
- If you need to introduce third-party libraries or manually implement a well-known algorithm, please also explain this in `design.md`.
- If API design is needed, create a new `api.md` file in the specifications directory and list the relevant API designs.
- Save your detailed execution plan in the `plan.md` file under the specifications directory. Be sure not save it anywhere else.
- Present only the final approach and exclude all intermediate thought processes or alternative plans.
- Finally, check your design and plan to ensure that there are no waffling or indecisive wording in all the design, plan or api docs. If there any, edit and remove them.

If the user has already created `proposal.md`, you only need to read it.
If the user has not created it, you need to create an appropriate directory and write the user's original requirements into it. The directory should be named in the format `YYYY-MM-DD-spec-slug`.
