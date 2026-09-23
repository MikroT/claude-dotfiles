# Global Operating Rules

1. **Commits**: Never create a git commit unless the user explicitly asks for it in that turn. Always follow Conventional Commits format (`type(scope): description`, lowercase imperative subject, under ~72 chars). Never include any AI attribution trailer ("Generated with", "Co-Authored-By", or similar) in commit messages or PR descriptions.

2. **Git safety**: Never run destructive git operations (force-push, reset --hard, checkout/restore/clean that discards uncommitted work, branch -D) without explicit user confirmation first. Run `git status` before anything that could discard uncommitted work.

3. **Secrets**: Never leave a generated or fetched secret (API key, password, SSH key) only in a local file, chat message, or committed config. Save it in Infisical and reference it via environment variable, never inline. When fetching an existing secret to use it, write it only to an ephemeral location (e.g. under `/tmp`), use it, delete it immediately after — never leave a standing local copy.

4. **Anonymization**: Before writing any real-world identifier (real company names, real emails, real tenant/client data) to disk in documentation, redact to generic placeholders (`<user 1>`, `example.com`) unless the file is explicitly meant to hold real production config.

5. **Verification**: Before declaring a task finished, verify it end-to-end when possible — run it, test it, read back the file just written — instead of assuming success from the last step's output.

6. **Before creating**: Check whether a file, script, config entry, or resource already exists before creating a new one — avoid silent duplicates.

7. **Code style**: Don't add features, abstractions, comments, or error handling beyond what the task actually requires. Comments only when the WHY is non-obvious (a hidden constraint, a workaround, something that would surprise a reader) — never comments that just restate what the code does.

8. **Language**: Default to responding in the language the user writes in (such as Italian, per user memory); rules 1-7 above apply regardless of conversation language.

9. **Language on disk**: Everything written to disk is in English, regardless of the conversation language — code, comments, commit messages, file/variable names, and all documentation. This is independent of rule 8 (rule 8 governs how you talk to the user; this rule governs what you write to files) and independent of what language existing files happen to use.

10. **Branching**: Most work (fixes, docs updates, small scripts, linear features) commits directly to the current default branch. Use a feature branch instead when a change is risky, exploratory, or multi-step (an architectural refactor, an experiment that might be abandoned) — branches allow free rollback without needing revert/reset on shared history. When in doubt about which category a task falls into, ask the user.

11. **Self-correction**: When the user corrects a mistake, don't just fix it inline and move on — propose adding a rule to this file (if general) or to the project's own context file (if project-specific) so the same mistake doesn't need correcting again. Always get the user's review before saving that new rule.

12. **Avoid backwards-compatibility hacks**: Don't rename unused variables to `_var`, re-export unused types, add `// removed` comments for deleted code, or otherwise leave traces of removed code "just in case." If you're certain something is unused, delete it completely.

13. **Proactive review**: After writing or modifying non-trivial code, ask whether to run `/review` (your own native review command) before it gets committed — the same way you already ask before committing. Don't run it unprompted, and don't skip asking just because the change seemed small.

14. **Model and task-splitting suggestions**: If a task's complexity clearly doesn't match the model currently in use (a trivial fix on a heavyweight model, or a genuinely hard task on a lightweight one), point it out and suggest switching models before proceeding — don't switch unprompted. If a task is substantial, independent of the current conversation, and doesn't need this session's context, suggest running it as a separate session/workspace instead of inline — say why (keeps this session focused, can run in parallel). Both are suggestions, not actions — proceed as asked unless the user opts in.

15. **Documentation currency**: For any project that keeps ADRs, runbooks, or a ROADMAP/PIPELINE file, keep them current at the natural trigger points — after closing an architectural decision, after a work session that changed how something works, when a README/index entry has gone stale. Don't invent new documentation structure the project doesn't already use.

16. **Keeping this file in sync**: When this file (/home/user/.qwen/QWEN.md) is modified, tell the user what changed and offer to sync it to /home/user/claude-dotfiles/qwen-code/QWEN.md and push, same as Claude Code's own CLAUDE.md is kept in sync there — always with explicit go-ahead per rule 1's commit policy.
