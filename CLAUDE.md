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

# Runbooks
In any project repo, if a `docs/runbooks/` folder exists, check it for a documented procedure (setup, disaster recovery, recurring maintenance) before improvising one from scratch. A runbook is the operational counterpart to an ADR: the ADR explains *why*, the runbook explains *how* — step by step, with real commands, never real credentials (use placeholders or point to where they're kept). When a significant infrastructure setup or recurring procedure gets nailed down (not routine work), propose writing or updating the corresponding `docs/runbooks/descriptive-name.md`, always with the user's review before committing. Unlike ADRs, runbooks are updated in place when the real procedure changes — they reflect current state, not history.

# Capturing session knowledge
Session knowledge that would otherwise only live in this conversation — a decision made, a constraint discovered, a workaround adopted — is exactly what an ADR or runbook is for. If something worth remembering surfaces that isn't already obvious from code or git history, treat it as a candidate for one of these docs, not just for your own working memory: memory helps you personally on this machine, but knowledge meant to survive and travel with the project belongs in its repo.

# Other doc patterns — common triggers
Beyond ADRs and runbooks, a few more patterns come up often enough to be worth recognizing on sight. Don't create any of these empty or as a precaution — propose creating one only once its trigger actually happens on the project at hand.

**Broadly applicable to most projects:**
- `docs/incidents/` — a post-mortem, written *after* something breaks in production: what went wrong, how it was fixed, what changes to prevent it. Reactive, unlike an ADR (proactive, before the fact).
  Trigger: the first real production incident on this project.
- `docs/glossary.md` — short definitions for project-specific domain terms.
  Trigger: a term is used ambiguously or inconsistently (different meaning in code vs. conversation) and it actually causes confusion.

**More situational — depend on how the project is run:**
- `docs/architecture/` (diagrams, system overview).
  Trigger: the system has grown complex enough that it no longer fits in one person's head from reading the code alone, or a new person needs onboarding to it.
- `CONTRIBUTING.md`.
  Trigger: someone other than the user and Claude starts contributing code to this project.
- `CHANGELOG.md`.
  Trigger: the project has external users/customers who need to know what changed release to release (not needed while it's internal-only).

# Projects index
The user keeps a running index of their projects at `github.com/MikroT/claude-dotfiles/projects-index.md` (purpose, status, where each one runs, repo link — context GitHub's own list doesn't show). When working on a project that looks new or significant (not a one-off script or throwaway task), check whether it's already listed there; if not, or if its status/details have changed, propose adding or updating its entry, with the user's review before committing. This keeps the index current as a byproduct of the work itself, not a separate chore — it won't catch projects never worked on in a session.

This file lives in `claude-dotfiles`, not in the project's own repo — it usually isn't already cloned in the current working directory. If it isn't, clone `claude-dotfiles` (or fetch the file directly, e.g. via the GitHub API or a temp clone) to read and update it, then commit and push there, same as any other change to that repo.

Only propose touching it for something that matters: a project missing entirely, or a real change in status/purpose/where it runs. Don't propose it just to bump a "last updated" date or for cosmetic wording — that's busywork across two repos for no real gain.

# Language
Respond to the user in Italian. Keep code, comments, commit messages, and file/variable names in English regardless of the conversation language.

# Commits
Never create a git commit unless the user explicitly asks for it in that turn. Staging files or preparing a diff is fine without asking; the commit itself always needs an explicit go-ahead.

# Before creating files, tables, or resources
Check whether it already exists first (file, DB table/column, cloud resource, config entry) before creating or writing it — avoid silent duplicates or overwrites. This applies especially to database schema changes and one-off scripts.

# Keeping claude-dotfiles in sync
This file, and the projects index it references, are backed up at github.com/MikroT/claude-dotfiles. When you add or change a rule here, or update an entry in projects-index.md, tell the user what changed and offer to sync it to that repo (clone if not already present locally, copy the file(s) over, commit, push — always with explicit go-ahead per the commit rule above).
