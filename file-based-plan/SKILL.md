---
name: file-based-plan
description: Use this skill when the user asks the Agent to read a proposal file under specs/ and **make a plan**, or when user referenced to this skill; It is not needed when the user asks to execute.
---

# File-Based Planning

First, please understand: `specs/<spec-name>` is the specifications directory, and the `proposal.md` file contains the user's original requirements.
Unless explicitly requested, the user's original requirements should not be modified.

## When user call this skill in prompt explicitly

The user wants you to create a plan; DO NOT modify any code. Check the argument of the skill:

- If the argument is a slug (which follows the format `YYYY-MM-DD-spec-slug`), that means the user has already created `proposal.md`, you need to read it instead of creating one.
- If not, the argument is user's proposal, you need to create an appropriate directory and write the user's original requirements into it. The directory should be named in the format `YYYY-MM-DD-spec-slug`. In this case, without explicitly mentioned or user approve, DO NOT merge the new requirements into any old specs you found, but keep them as a reference in new spec.

Then follows the instructions below to draft a plan.

## When you need to draft a plan

- Explore the code and plan the system design. Ask the user about anything unclear to clarify the design details, trade-offs, and context.
- Create a new `design.md` file in the specifications directory and briefly describe your system and architectural design.
- If you need to introduce third-party libraries or manually implement a well-known algorithm, please also explain this in `design.md`.
- If API design is needed, create a new `api.md` file in the specifications directory and list the relevant API designs.
- Save your detailed execution plan in the `plan.md` file under the specifications directory. Be sure not save it anywhere else.
- If the user provides additional information during the planning or execution process, or if you obtain any supplementary details or extra context by asking the user, you must update the `proposal.md` file accordingly. If the user makes corrections to the proposal, directly modify the relevant sections in `proposal.md` instead of appending new, conflicting, or indecisive entries. This can be considered as explicitly requested modifications to the user's original requirements so modifications is allowed.
- Present only the final approach and exclude all intermediate thought processes or alternative plans.
- Finally, check your design and plan to ensure that there are no waffling or indecisive wording in all the design, plan or api docs. If there any, edit and remove them.

## When making the system design

- **No unsolicited compatibility layers.** DO NOT introduce compatibility shims, fallbacks, deprecated-path branches, or any code whose purpose is to preserve old behavior — unless the user explicitly asks for it. Prefer clean replacements that update all call sites. If you genuinely believe a compatibility layer is unavoidable or strongly warranted, **stop and ask the user before writing it**.
- **Fail fast on input; do not be lenient.** DO NOT add forgiving normalization of user/API input — no silent trimming of whitespace, no case-folding, no coercing empty strings to defaults, no "did you mean" guessing, no accepting near-miss formats. Validate strictly and reject invalid input with a clear error. Only relax this when the user explicitly asks for it.
