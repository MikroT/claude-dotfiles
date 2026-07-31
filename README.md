# claude-dotfiles

[English](#english) · [Italiano](#italiano)

---

## English

Personal global config for Claude Code — rules that apply to every project I work on, not tied to a single repo.

### Setup on a new machine (or after a reset)

1. Generate a dedicated SSH key for this repo — don't reuse keys from other projects/servers:
   ```
   ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519_dotfiles -N '' -C 'claude-dotfiles-<machine-name>'
   ```
2. Add a host alias in `~/.ssh/config`:
   ```
   Host github-dotfiles
     HostName github.com
     User git
     IdentityFile ~/.ssh/id_ed25519_dotfiles
     IdentitiesOnly yes
   ```
3. Copy the public key (`cat ~/.ssh/id_ed25519_dotfiles.pub`) and add it on GitHub: repo → **Settings → Deploy keys → Add deploy key**. Only tick **Allow write access** if you plan to push updates from this machine — otherwise leave it read-only.
4. Verify authentication: `ssh -T github-dotfiles` should reply "Hi MikroT/claude-dotfiles! ... does not provide shell access" (the shell error is expected, it just confirms the key works).
5. Clone and restore:
   ```
   git clone github-dotfiles:MikroT/claude-dotfiles.git
   cp claude-dotfiles/CLAUDE.md ~/.claude/CLAUDE.md
   ```
   If `~/.claude/CLAUDE.md` already exists with other content, merge by hand instead of overwriting.

### Keeping it up to date

When a new general rule/preference (not project-specific) comes up during a session, it gets added to `~/.claude/CLAUDE.md` on the machine in use, then synced here with a commit — it's the same file, not a separate process. The global `~/.claude/CLAUDE.md` includes an explicit reminder to offer this sync whenever a new rule is added, so it doesn't need to be remembered manually.

### What never goes in this repo

Never credentials, `.credentials.json`, `settings.json` (contains permission rules tied to local paths, not portable), `history.jsonl`, or the `projects/` folder (holds per-project memory, potentially sensitive client data). Only generic behavioral config.

---

## Italiano

Config personale globale per Claude Code — regole valide per ogni progetto su cui lavoro (non specifiche di un singolo repo).

### Setup su un nuovo PC (o dopo un reset)

1. Genera una SSH key dedicata a questo repo — non riusare chiavi di altri progetti/VPS:
   ```
   ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519_dotfiles -N '' -C 'claude-dotfiles-<nome-macchina>'
   ```
2. Aggiungi un host alias in `~/.ssh/config`:
   ```
   Host github-dotfiles
     HostName github.com
     User git
     IdentityFile ~/.ssh/id_ed25519_dotfiles
     IdentitiesOnly yes
   ```
3. Copia la chiave pubblica (`cat ~/.ssh/id_ed25519_dotfiles.pub`) e aggiungila su GitHub: repo → **Settings → Deploy keys → Add deploy key**, spunta **Allow write access** solo se prevedi di pushare aggiornamenti da questa macchina, altrimenti lasciala di sola lettura.
4. Verifica l'autenticazione: `ssh -T github-dotfiles` deve rispondere "Hi MikroT/claude-dotfiles! ... does not provide shell access" (l'errore di shell è normale, conferma solo che la chiave funziona).
5. Clona e ripristina:
   ```
   git clone github-dotfiles:MikroT/claude-dotfiles.git
   cp claude-dotfiles/CLAUDE.md ~/.claude/CLAUDE.md
   ```
   Se `~/.claude/CLAUDE.md` esiste già con altro contenuto, unire a mano invece di sovrascrivere.

### Come mantenerlo aggiornato

Quando durante una sessione emerge una nuova regola/preferenza generale (non specifica di un progetto), va aggiunta a `~/.claude/CLAUDE.md` sulla macchina in uso, poi sincronizzata qui con un commit — non è un processo separato, è lo stesso file. Il `~/.claude/CLAUDE.md` globale include un promemoria esplicito di proporre questa sincronizzazione quando si aggiunge una nuova regola, così non serve ricordarselo manualmente.

### Cosa NON va in questo repo

Mai credenziali, `.credentials.json`, `settings.json` (contiene permission rule legate a path locali specifici, non portabili), `history.jsonl`, o la cartella `projects/` (contiene memoria specifica di singoli progetti, potenzialmente dati sensibili di clienti). Solo config comportamentale generica.
