# graphify
- **graphify** (`~/.claude/skills/graphify/SKILL.md`) - any input to knowledge graph. Trigger: `/graphify`
When the user types `/graphify`, invoke the Skill tool with `skill: "graphify"` before doing anything else.

# Cross-project knowledge graph
The user maintains a cross-project knowledge graph at `github.com/MikroT/knowledge-graphify`, built with graphify over their active repos (currently `awra`, `mcm`, `synology-backup-reports`, `proxmox-infrastructure`) and synced across machines via plain `git pull`/`git push` — not a live service (see that repo's `docs/decisions/0001-git-sync-not-live-service.md` for why). It does not replace ADRs/runbooks, which remain the source of truth in each project's own repo — it's a derived, regenerable index that answers cross-project questions ("what have we decided about X across every project that touches it") without manually reading through several repos by hand.

At the end of a work block that added or meaningfully changed indexed content in one of the covered repos (same trigger as the README-freshness rule below), propose updating the graph — `git pull` in `knowledge-graphify`, re-run `graphify <repo-urls> --update`, commit, push — with the user's review before committing, same as any other doc update. "Indexed content" means an ADR or runbook (added, changed, or superseded), or another document meant to be a retrievable answer to a cross-project question (e.g. a root-level `ROADMAP.md`/`PIPELINE.md` summary, per that convention above) — not just files literally named ADR/runbook. Don't propose it for routine/small changes (a typo fix, a pure file move with no content change, cosmetic wording) — same spirit as the other doc-trigger rules in this file. If a new project reaches the point of having its own `docs/decisions/`, propose adding it to the graph's scope.

# Agno agent development
When a project uses [Agno](https://www.agno.com/) as its agent framework, before writing any Agno-related code — anything the framework could plausibly support or already provide, not just the obvious cases (middleware, auth, tools, session/memory handling are examples, not an exhaustive list): read Agno's own documentation and code examples directly via the `agno-docs` MCP server (`docs.agno.com/mcp` — `search_agno` for broad/conceptual lookups, `query_docs_filesystem_agno` for reading specific pages or grepping across the docs) before writing the code, rather than relying on memory or assumption. This has repeatedly turned out to matter — Agno is a mature framework and often already solves problems that looked like they needed custom code (example: AgentOS ships a built-in `AuthMiddleware` for JWT validation, claims extraction, and `user_isolation` — no need to hand-roll that). Note: this MCP server is documentation-only (read-only, no code generation, no actions) — it's a research tool, not a substitute for writing the code yourself grounded in what it returns. If the docs are ambiguous or silent on a specific point after a real search (not just one query), say so explicitly and flag the uncertainty to the user rather than silently falling back to assumption — same principle as not inventing graph edges or fabricating results elsewhere in this file.

Registration is per-project/per-directory (`claude mcp add --transport http agno-docs https://docs.agno.com/mcp`), not global — check `claude mcp list` first when working in an Agno project, and register it yourself if missing rather than waiting for it to become a blocker.

# Architecture Decision Records (ADR)
The first time a project is touched in a session (not on every message after that), check whether `docs/decisions/` exists and skim the list of ADRs there — don't wait for the task to look "architectural" before checking. This is what surfaced a real gap once: a fresh session had no way to know where infrastructure credentials lived or that a Synology DSM API existed as an alternative to SSH+sudo, and had to rediscover both mid-session instead of just reading the existing runbook (see `proxmox-infrastructure`'s `docs/runbooks/credentials-and-secrets-access.md`, added specifically so this doesn't repeat). A quick skim at the start costs little; rediscovering settled context mid-task costs a lot more.

**Same first-touch pass, whichever of these exist:** `README.md` (what the project is, current status), `ROADMAP.md`/`PIPELINE.md` (if the project has reached that point — see their own section below), and `docs/notes/session-notes.md` (if coding work has started — see below). One pass, not four separate checks: skim the root-level files and `docs/` structure once, at the start, before diving into the task.

Beyond that first skim, read the relevant ADRs there before working on architectural or design tasks — don't re-litigate decisions already recorded. When closing an important architectural decision (a choice that changes system structure, or discards a real alternative after discussion — not routine changes), propose creating a new ADR at `docs/decisions/NNNN-short-title.md` following this format:

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
Same first-touch check as ADRs above: skim `docs/runbooks/` (if it exists) the first time a project is opened in a session, not only once a specific procedure is needed — a runbook that explains where credentials live or how to reach a piece of infrastructure is exactly the kind of thing that's cheap to read upfront and expensive to reconstruct mid-task by asking the user to re-explain it. Beyond that, check it for a documented procedure (setup, disaster recovery, recurring maintenance) before improvising one from scratch. A runbook is the operational counterpart to an ADR: the ADR explains *why*, the runbook explains *how* — step by step, with real commands, never real credentials (use placeholders or point to where they're kept). When a significant infrastructure setup or recurring procedure gets nailed down (not routine work), propose writing or updating the corresponding `docs/runbooks/descriptive-name.md`, always with the user's review before committing. Unlike ADRs, runbooks are updated in place when the real procedure changes — they reflect current state, not history.

Hands-on exploration of a third-party API or tool falls under this too: when hitting the real thing — via `curl`, an SDK, or capturing a browser's actual request via DevTools — regularly surfaces something no public documentation states (an exact payload shape, an enum's valid values, a required-but-undocumented follow-up call, an environment quirk, a transient-vs-permanent error pattern), that belongs in a runbook (e.g. `docs/runbooks/<service>-api-usage.md`), not just in the conversation or the ADR that explains why the tool was chosen. Write the finding down as soon as it's confirmed, not batched at the end of a session — same "capture it as it happens" principle as the section below, applied to API/tool behavior specifically, not just infrastructure decisions.

# Capturing session knowledge
Session knowledge that would otherwise only live in this conversation — a decision made, a constraint discovered, a workaround adopted — is exactly what an ADR or runbook is for. If something worth remembering surfaces that isn't already obvious from code or git history, treat it as a candidate for one of these docs, not just for your own working memory: memory helps you personally on this machine, but knowledge meant to survive and travel with the project belongs in its repo.

Capture it as it happens, not only when asked at the end of a work block. A long session that touches infrastructure/config directly (not just source code — SSH into servers, cloud consoles, hardware setup) accumulates decisions and dead-ends faster than they're easy to reconstruct afterward: a discarded approach and *why* it was discarded, a root cause found after a wrong turn, a constraint that shaped a choice. Waiting until the user asks "document this" risks reconstructing the session from memory and missing pieces — as happened once, where a documentation pass written after the fact needed a second pass to add back a discarded remote-install approach and a decision made mid-session that hadn't made it into any file yet. When a real decision or workaround lands mid-session, note it (a scratch file, or straight into the relevant ADR/runbook draft if one's already open) instead of trusting it'll be remembered accurately once the session moves on.

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

# ROADMAP.md and PIPELINE.md at repo root
Once a project has a real roadmap decision (an ADR sequencing phases of work, e.g. `docs/decisions/NNNN-*-roadmap.md`) and/or a real development pipeline decided (repo structure, stack, test/CI — usually spread across a few ADRs), mirror them as short, root-level summary files: `ROADMAP.md` and `PIPELINE.md`, uppercase per the `README.md`/`LICENSE` convention. These are pointers, not the source of truth — each summary file explicitly says so and links back to the ADR(s) it summarizes; the ADR keeps its full Context/Decision/Alternatives-considered/Open-questions shape and is what gets updated when the decision itself changes. Update the root summary only when the phases or pipeline shape actually change, not for every ADR edit.

Trigger for creating them: propose creating these only once the underlying ADR(s) actually exist and are stable enough to summarize — not preemptively, same spirit as the "Other doc patterns" triggers below. If only one of the two exists yet (e.g. a pipeline decided but no phased roadmap), create only that one file.

Trigger for keeping them current, once they exist: check both at the same work-block boundary as the README-freshness check below (a tracked block of work wraps up, a milestone/feature is done) — don't wait to be asked each time. If a roadmap phase completed, changed, or reordered, or the pipeline's structure/stack/test strategy changed (a new or superseding ADR), propose the corresponding ROADMAP.md/PIPELINE.md update then, same as any other doc update: the underlying ADR is never edited in place (a new ADR supersedes it, per the ADR rule above), only the root summary file is updated to point at the new ADR and reflect the new state.

# README freshness
When a tracked block of work wraps up (a todo list's items all complete, a milestone/feature is done), check whether the project's README still matches reality — status sections like "scaffold" or "in progress" left over from early on are the most common staleness. If it's stale, propose an update (and, if relevant, a pointer to `docs/decisions/` and `docs/runbooks/` for newcomers), with the user's review before committing. Don't bump the README for every small change — only at natural work-block boundaries, same spirit as the ADR/runbook triggers above.

# Projects index
The user keeps a running index of their projects at `github.com/MikroT/claude-dotfiles/projects-index.md` (purpose, status, where each one runs, repo link — context GitHub's own list doesn't show). When working on a project that looks new or significant (not a one-off script or throwaway task), check whether it's already listed there; if not, propose adding it, with the user's review before committing.

**Staleness check — same trigger as README freshness above, not a separate one to remember.** "If its status/details have changed" only catches drift if something actively prompts a look — for 10 days across several substantial work blocks (TacticalRMM's full production migration, Infisical going live, UrBackup reaching 4 real clients) nothing did, and the index sat describing "first node, first VM" long after that was no longer true. So: at the same work-block boundary where README freshness gets checked (a tracked block of work wraps up, a milestone/feature is done), also check this project's own row in the index against its current real state, not just the README. If they've drifted apart, propose updating the row, with the user's review before committing — same trigger, same moment, just one more file to glance at.

This file lives in `claude-dotfiles`, not in the project's own repo — it usually isn't already cloned in the current working directory. If it isn't, clone it into a scratch/temp location (never inside the project you're currently working on) to read and update it. If you'll likely need `claude-dotfiles` again later in the same session (e.g. both the index and a CLAUDE.md rule change), keep that clone for the rest of the session; otherwise remove it once pushed, same cleanup discipline as any other one-off script.

Only propose touching it for something that matters: a project missing entirely, or a real change in status/purpose/where it runs. Don't propose it just to bump a "last updated" date or for cosmetic wording — that's busywork across two repos for no real gain.

`claude-dotfiles` itself is listed in its own index — since editing it doesn't feel like "visiting another project," it's easy to forget its entry when something about it materially changes (not for every commit). Treat it the same as any other project for this rule.

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

# Session notes for coding work (docs/notes/)
ADRs and runbooks capture decisions and procedures — they deliberately don't capture day-to-day implementation progress (what got built this session, what's half-done, what broke and why, what to pick up next). For that, use `docs/notes/session-notes.md` (single running file, not one file per session) once real code is being written (not during pure design/ADR work — that's what ROADMAP.md/PIPELINE.md already track).

At the end of a coding work block (not every message — same work-block-boundary spirit as the README-freshness rule), update it yourself with: what was done, what's in progress or blocked, and a concrete next step. Do this proactively, without being asked — the entire point is that the user shouldn't have to paste a summary from one session (or one model/tool) into the next; the file does that handoff instead. Keep entries terse and dated (`## 2026-08-26`), newest on top. Prune or fold entries once their content is fully captured by a merged commit/ADR/runbook and no longer needed as working memory — this file is short-term operational memory, not a permanent history (git log and ADRs are the permanent record).

At the start of a coding session, read this file (if it exists) as part of the first-touch check alongside `docs/decisions/` and `docs/runbooks/` — it's the fastest way to resume exactly where the last session left off without the user re-explaining it.

# Keeping claude-dotfiles in sync
This file, and the projects index it references, are backed up at github.com/MikroT/claude-dotfiles. When you add or change a rule here, or update an entry in projects-index.md, tell the user what changed and offer to sync it to that repo — same clone/commit/push mechanics as the Projects index section above, always with explicit go-ahead per the commit rule above.
