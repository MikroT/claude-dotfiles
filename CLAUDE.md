# graphify
- **graphify** (`~/.claude/skills/graphify/SKILL.md`) - any input to knowledge graph. Trigger: `/graphify`
When the user types `/graphify`, invoke the Skill tool with `skill: "graphify"` before doing anything else.

# Documentation workflow
For ADRs, runbooks, session notes, ROADMAP/PIPELINE, README/projects-index freshness, the cross-project knowledge graph, and Agno/MCP documentation lookups — invoke the `docs-workflow` skill (`~/.claude/skills/docs-workflow/SKILL.md`) rather than relying on memory of these rules. That skill's own description lists its triggers (first project touch in a session, closing an architectural decision, end of a work block, etc.) — invoke it proactively when one of those triggers fires, don't wait to be asked.

# Language
Everything written to disk is in English, regardless of the conversation language: code, comments, commit messages, file/variable names, and all project documentation (ADR, runbooks, README, architecture docs, and any other markdown). This is independent of what language the repo's existing docs happen to be in — English is the fixed target, not "match what's already there."

# Commits
Never create a git commit unless the user explicitly asks for it in that turn. Staging files or preparing a diff is fine without asking; the commit itself always needs an explicit go-ahead.

# Conventional Commits
Every commit subject line follows [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/): `<type>[optional scope]: <description>`. Type is one of the Angular-convention set (the de facto standard beyond the spec's bare-minimum `feat`/`fix`): `feat` (new capability), `fix` (bug fix), `docs` (documentation only — ADRs, README, runbooks, ROADMAP/PIPELINE), `chore` (tooling, dependency bumps, repo housekeeping, cross-repo sync commits), `refactor` (code change that's neither a fix nor a feature), `test` (adding/correcting tests), `ci` (CI/CD config), `style` (formatting, no logic change), `perf` (performance improvement). Scope (optional) is a short parenthetical noting the affected area, e.g. `docs(adr): add ADR 0024`. Breaking changes get a `!` after the type/scope (`feat!: ...`) or a `BREAKING CHANGE:` footer.

Description is imperative mood, lowercase, no trailing period, under ~72 chars — `add ROADMAP.md and move PIPELINE.md to root`, not `Added ROADMAP.md.`. The commit body (below the subject) keeps explaining the *why*, same as before this rule — Conventional Commits standardizes the subject line's structure, not the reasoning in the body. Applies to every repo this user works in, not just projects that already use the convention — introduce it on the next commit rather than waiting for a project-specific trigger.

# Branching
Solo developer, no team, no PR review — so branch protection has low value by default, and most work (fixes, docs updates, small scripts, linear features) commits directly to `main`/`master`. Use a feature branch instead when a change is risky, exploratory, or multi-step (e.g. an architectural refactor, an experiment that might be abandoned entirely) — branches give free rollback (discard the branch, `main` stays untouched) without needing `revert`/`reset` on shared history. No need for a heavier flow (develop/release/hotfix branches) — a plain feature branch merged back to `main` when stable is enough. When in doubt about which category a task falls into, ask.

# Anonymize real-world data in committed documentation
When a spike, test, or validation run uses real accounts/tenants/data (a client's real Google Workspace or Microsoft 365 tenant, real email addresses, real company names or domains — not synthetic test fixtures like `mario@awra-spike.test`), never write those real identifiers into any file destined for a repo (ADRs, runbooks, status notes, commit messages, code comments) — always redact to generic placeholders (`<user 1>`, `example.com`, "an admin account") before writing to disk, preserving whatever the placeholder needs to convey (count, role, outcome) without the real identity. This applies even when the repo is private — a private repo is not a safe place for a client's real data, and anonymization has to happen at write time, not fixed after the fact, since the real names may already be committed and pushed before anyone notices. If real identifiers already made it into unstaged/uncommitted files, redact before staging; if they're already committed, flag it to the user explicitly rather than silently rewriting history (history rewrites are a destructive operation the user must decide on, not something to do unprompted).

# Before creating files, tables, or resources
Check whether it already exists first (file, DB table/column, cloud resource, config entry) before creating or writing it — avoid silent duplicates or overwrites. This applies especially to database schema changes and one-off scripts.

# Verification
Before declaring a task finished, verify it end-to-end whenever possible instead of assuming it worked from the last step's output alone: run it, test it, check the logs, read back the file you just wrote. A task that touched infrastructure or a live system isn't done until its actual effect has been observed, not just the command that was supposed to cause it.

# Self-correction
When the user corrects a mistake, don't just fix it inline and move on — propose adding a rule to this file (if it's a general behavior) or to the project's own `CLAUDE.md` (if it's project-specific) so the same mistake doesn't need correcting again next time. Always with the user's review before committing, same as any other doc change.

# Keeping claude-dotfiles in sync
This file, the skills under `~/.claude/skills/` referenced above, and the projects index are backed up at github.com/MikroT/claude-dotfiles. When you add or change a rule here, change the `docs-workflow` skill, or update an entry in projects-index.md, tell the user what changed and offer to sync it to that repo — same clone/commit/push mechanics described in that repo's README, always with explicit go-ahead per the Commits rule above.
