# Moving a Claude Code session to another machine

How to bring a specific conversation — not just the project code —
to a different machine, e.g. onto a persistent host like
`claude-code-host` instead of a laptop. This is a direct
machine-to-machine copy of files; it never goes through this repo or
any git remote (see the main [README](../../README.md)'s "What never
goes in this repo" — session files can hold sensitive client data and
must never be committed anywhere).

## 1. Find the session

Every conversation is one file:
```
~/.claude/projects/<project-path-with-slashes-as-dashes>/<session-id>.jsonl
```
The path encoding replaces every `/` with `-` — `/home/user/repos/mcm`
becomes `-home-user-repos-mcm`. List that directory (or check the
VS Code sidebar's session picker, which shows human-readable titles) to
find the right `<session-id>`. Sort by mtime/size if you need "the
most recent one" rather than a specific id you already have.

A session may also have a same-named subfolder
(`<session-id>/`, holding tool-result attachments) — check for it, not
every session has one.

## 2. Copy the files across

Preserve the exact same path-encoded directory structure on the
destination — the destination directory won't exist yet for a project
never touched on that machine before, so `mkdir -p` it first:

```bash
mkdir -p <destination-host>:~/.claude/projects/<same-encoded-path>/   # if new on that machine
scp <session-id>.jsonl <destination-host>:~/.claude/projects/<same-encoded-path>/
scp -r <session-id>/ <destination-host>:~/.claude/projects/<same-encoded-path>/   # only if that subfolder exists
```

**Project memory (`memory/`) is a separate thing** — persistent,
cross-session notes, distinct from any one conversation's history.
Copy it too if continuity matters, but decide on it independently of
which session(s) you're moving:
```bash
scp -r memory/ <destination-host>:~/.claude/projects/<same-encoded-path>/
```

## 3. Resume the exact conversation

From inside that project's directory on the destination machine:
```bash
claude --resume <session-id>
```
Not `claude --resume` alone — with no id, that opens an interactive
picker whenever 2+ sessions exist for that project. Fine for a human at
a terminal; breaks anything meant to run unattended (a cron job, a
phone SSH client, a dashboard script) since nothing is there to answer
the picker. Always pass the id explicitly for anything non-interactive.

## 4. Register it in a multi-project dashboard, if the destination has one

A dashboard that opens one tmux window per (project, session) pair,
each pinned to `claude --resume <session-id>`, needs the transferred
session added to its own entries list — see
`proxmox-infrastructure`'s
[claude-code-host-persistent-sessions.md](https://github.com/MikroT/proxmox-infrastructure/blob/main/docs/runbooks/claude-code-host-persistent-sessions.md)
for that specific VM's dashboard script and entry format
(`window-name|repo-subdir|session-id`). Not every destination has a
dashboard — skip this step for a plain single-session machine.

## Notes

- There's no bulk "sync everything" step. Each transfer is a
  deliberate choice of which conversation(s) to carry over — copying
  every session for every project indiscriminately defeats the point
  of a clean, purpose-built destination environment.
- The same project can have more than one session transferred (e.g.
  two different ongoing conversations about the same repo) — each gets
  its own dashboard entry with its own window name, since they're
  otherwise indistinguishable by directory alone.
- If the destination machine has never had Claude Code point at this
  project directory before, the very first `claude --resume` there
  still needs the actual project files present (a `git clone`, not
  just the `.claude/projects/` session data) — this runbook only
  covers the conversation history/memory, not the code itself.
