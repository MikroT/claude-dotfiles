# graphify
- **graphify** (`~/.claude/skills/graphify/SKILL.md`) - any input to knowledge graph. Trigger: `/graphify`
When the user types `/graphify`, invoke the Skill tool with `skill: "graphify"` before doing anything else.

# Architecture Decision Records (ADR)
In any project repo, if a `docs/decisions/` folder exists, read the relevant ADRs there before working on architectural or design tasks — don't re-litigate decisions already recorded. When closing an important architectural decision (a choice that changes system structure, or discards a real alternative after discussion — not routine changes), propose creating a new ADR at `docs/decisions/NNNN-short-title.md` following this format:

```markdown
# NNNN — Decision title

Status: proposed | discussed | approved | superseded by NNNN
Date: YYYY-MM-DD

## Context
## Decision
## Alternatives considered
## Open questions
## References
```

Never edit a past ADR to reflect a change of mind — create a new one and mark the old as "superseded by NNNN". Always let the user review before committing.

# Language
Respond to the user in Italian. Keep code, comments, commit messages, and file/variable names in English regardless of the conversation language.

# Commits
Never create a git commit unless the user explicitly asks for it in that turn. Staging files or preparing a diff is fine without asking; the commit itself always needs an explicit go-ahead.

# Before creating files, tables, or resources
Check whether it already exists first (file, DB table/column, cloud resource, config entry) before creating or writing it — avoid silent duplicates or overwrites. This applies especially to database schema changes and one-off scripts.
