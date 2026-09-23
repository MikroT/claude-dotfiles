# Global Operating Rules

1. **Commits**: Never create a git commit unless the user explicitly asks for it in that turn. Always follow Conventional Commits format (`type(scope): description`, lowercase imperative subject, under ~72 chars). Never include any AI attribution trailer ("Generated with", "Co-Authored-By", or similar) in commit messages or PR descriptions.

2. **Git safety**: Never run destructive git operations (force-push, reset --hard, checkout/restore/clean that discards uncommitted work, branch -D) without explicit user confirmation first. Run `git status` before anything that could discard uncommitted work.

3. **Secrets**: Never leave a generated or fetched secret (API key, password, SSH key) only in a local file, chat message, or committed config. Save it in Infisical and reference it via environment variable, never inline. When fetching an existing secret to use it, write it only to an ephemeral location (e.g. under `/tmp`), use it, delete it immediately after — never leave a standing local copy.

4. **Anonymization**: Before writing any real-world identifier (real company names, real emails, real tenant/client data) to disk in documentation, redact to generic placeholders (`<user 1>`, `example.com`) unless the file is explicitly meant to hold real production config.

5. **Verification**: Before declaring a task finished, verify it end-to-end when possible — run it, test it, read back the file just written — instead of assuming success from the last step's output.

6. **Before creating**: Check whether a file, script, config entry, or resource already exists before creating a new one — avoid silent duplicates.

7. **Code style**: Don't add features, abstractions, comments, or error handling beyond what the task actually requires. Comments only when the WHY is non-obvious (a hidden constraint, a workaround, something that would surprise a reader) — never comments that just restate what the code does.

8. **Language**: Default to responding in the language the user writes in (such as Italian, per user memory); rules 1-7 above apply regardless of conversation language.
