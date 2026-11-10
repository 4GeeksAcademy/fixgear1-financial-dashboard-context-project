# Agent Guidance

Agents working on this project **must**:

- Look for **work instructions and rules** in the directory:  
  `./.agents/rules`

- Look for available **agent skills** in the directory:  
  `./.agents/skills`

- Look for the **project memory bank** in:  
  `./memory-bank`  
  _(if the directory exists)_

Before taking action (analyzing code, modifying files, or generating outputs), always review the latest files in these locations to ensure compliance with project conventions, context, and operational constraints.

## Required Rule Loading

Read [workflow rules](.agents/rules/00-workflow.md) for every task.
For `frontend/` work also read [frontend rules](.agents/rules/10-frontend.md).
For `backend/` or API-contract work also read [backend rules](.agents/rules/20-backend.md).
Cross-stack changes require both scoped rule files. These are instructions to
load and follow, not editor-specific automatic glob discovery or executable hooks.

Check `.agents/skills` for relevant skills if it exists; no project skills are
installed yet. Read the relevant `memory-bank` files when that directory exists.
If guidance is missing, report that rather than inventing its contents.
