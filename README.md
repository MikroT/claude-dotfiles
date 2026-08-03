# claude-dotfiles

[English](#english) · [Italiano](#italiano)

---

## English

Personal global config for Claude Code, and the full onboarding guide for setting up a new machine from scratch — install, restore preferences, reconnect to existing project repos, and know exactly what does *not* come back automatically.

### 0. Prerequisites

- A terminal (macOS/Linux native, or WSL on Windows)
- `git` installed
- An Anthropic account with Claude Code access

### 1. Install Claude Code

Follow the official install instructions for your OS at [claude.com/claude-code](https://claude.com/claude-code). Run `claude` once and log in — this creates the base `~/.claude/` folder.

### 2. Restore the global CLAUDE.md (personal behavior)

This is the file that makes every session, on any project, behave the way you expect: respond in Italian, ask before committing, read ADRs before architecture decisions, and so on.

> Anything in `<angle brackets>` from here on is a placeholder — replace it with your real value before running the command, don't paste it as-is.

**Do steps 2.1–2.6 yourself, without Claude** — a fresh Claude Code session on a new machine can't read this file yet (it isn't cloned, and this repo is private, so there's no shortcut like "hey Claude, go read the README on GitHub"). Once step 2.6 finishes and the repo is cloned locally, *then* you can open Claude Code in that folder and say "read this README and finish the remaining steps" — from there on it can help.

```bash
# 2.1 — generate a key dedicated to this repo (never reuse a key from another project/server)
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519_dotfiles -N '' -C 'claude-dotfiles-<machine-name>'
```

```bash
# 2.2 — add a host alias so git uses that key only for this repo
cat >> ~/.ssh/config << 'EOF'

Host github-dotfiles
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_dotfiles
  IdentitiesOnly yes
EOF
chmod 600 ~/.ssh/config
```

```bash
# 2.3 — print the public key, copy it
cat ~/.ssh/id_ed25519_dotfiles.pub
```

2.4 — On GitHub: this repo → **Settings → Deploy keys → Add deploy key** → paste it. Tick **Allow write access** only if you'll push updates from this machine; otherwise leave it read-only.

```bash
# 2.5 — verify the key works (the "shell access" error is expected, it just confirms auth)
ssh -T github-dotfiles
```

```bash
# 2.6 — clone and restore
git clone github-dotfiles:MikroT/claude-dotfiles.git
cp claude-dotfiles/CLAUDE.md ~/.claude/CLAUDE.md
```

If `~/.claude/CLAUDE.md` already has other content on this machine, merge by hand instead of overwriting.

**Checkpoint:** open `claude` on any empty folder and ask something that should trigger a rule (e.g. "create a file X") — it should ask before creating, and answer you in Italian.

### 3. Reconnect to an existing project (example: MCM)

Each project repo needs its **own** dedicated key — deploy keys are per-repo, keys are never shared across projects.

**Same rule as above** — generate the key, add it as a Deploy Key, verify it, and clone, all yourself. Claude can't fetch a private repo it has no key for. Once the clone succeeds, it can pick up from the project's own `CLAUDE.md` and `docs/`.

```bash
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519_mcm -N '' -C 'mcm-<machine-name>'

cat >> ~/.ssh/config << 'EOF'

Host github-mcm
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_mcm
  IdentitiesOnly yes
EOF

cat ~/.ssh/id_ed25519_mcm.pub
```

Add it as a Deploy Key (with write access if you'll push) on the project's GitHub repo, verify with `ssh -T github-mcm`, then:

```bash
git clone github-mcm:MikroT/mcm.git
```

You get the code back **and** `docs/decisions/` (the project's Architecture Decision Records) **and** `docs/runbooks/` (its operational procedures) **and** its own project-level `CLAUDE.md` — all versioned, all restored automatically by the clone.

Repeat this whole step, with its own key and alias, for every other project repo you work on.

**New project with no documentation yet?** Use [`docs-standard-setup-prompt.md`](docs-standard-setup-prompt.md) — paste it into that project's Claude Code session to bootstrap the full documentation standard there (ADR, runbooks, and the trigger-based patterns), including retroactive reconstruction from git history and existing docs.

### 4. If the project lives on a remote server (example: MCM's VPS)

This is separate from GitHub access — it's SSH access to the server itself where the app actually runs and gets deployed from.

```bash
ssh-keygen -t ed25519 -f ~/.ssh/keys/<server-alias> -N ''
cat >> ~/.ssh/config << 'EOF'

Host <server-alias>
  HostName <server-ip>
  User <server-user>
  Port <server-port>
  IdentityFile ~/.ssh/keys/<server-alias>
EOF
```

Send the **public** key (`.pub`) to whoever manages the server so it gets added to `~/.ssh/authorized_keys` there — this is manual, there's no automatic sync for server access.

### 5. What does NOT come back automatically

Be deliberate about these — none of them are covered by cloning a repo:

| What | Why it doesn't sync | What to do |
|---|---|---|
| Claude's per-project memory (`~/.claude/projects/*/memory/`) | Lives only on the machine that wrote it, not in any repo | Back up that folder manually if continuity matters, or let it rebuild over time on the new machine |
| Server/VPS SSH access | Independent of GitHub, tied to that server's `authorized_keys` | Repeat step 4 for each server, coordinate with whoever administers it |
| API keys (Gemini, Anthropic console, provider keys used by the app) | Deliberately never stored in any repo | Re-enter them by hand in the app's `.env` / secrets manager on first deploy from the new machine |
| `settings.json` (Claude Code local permission rules) | Full of machine-specific paths, not portable | Rebuild organically as you approve permissions on the new machine — don't copy the old one over |

### 6. Keeping this repo up to date

When a new general rule/preference comes up in any session, it gets added to `~/.claude/CLAUDE.md` on the machine in use, then synced here with a commit — same file, not a separate process. The global `CLAUDE.md` itself carries a standing reminder to offer this sync whenever a rule changes, so it's never left to memory.

### 7. Projects index

[`projects-index.md`](projects-index.md) tracks every project worked on with Claude Code — purpose, status, where it runs, repo link. It's updated as a byproduct of working on a given project (the global `CLAUDE.md` carries the reminder), not by a separate manual chore or continuous scan.

### What never goes in this repo

Never credentials, `.credentials.json`, `settings.json`, `history.jsonl`, or the `projects/` folder (per-project memory, potentially sensitive client data). Only generic behavioral config.

---

## Italiano

Config personale globale per Claude Code, e guida completa di onboarding per configurare un PC nuovo da zero — installazione, ripristino preferenze, riconnessione ai repo di progetto esistenti, e cosa esattamente *non* torna in automatico.

### 0. Prerequisiti

- Un terminale (nativo su macOS/Linux, o WSL su Windows)
- `git` installato
- Un account Anthropic con accesso a Claude Code

### 1. Installa Claude Code

Segui le istruzioni ufficiali per il tuo sistema operativo su [claude.com/claude-code](https://claude.com/claude-code). Avvia `claude` una volta e fai login — questo crea la cartella base `~/.claude/`.

### 2. Ripristina il CLAUDE.md globale (comportamento personale)

È il file che fa comportare ogni sessione, su qualunque progetto, come ti aspetti: risponde in italiano, chiede conferma prima di committare, legge gli ADR prima di decisioni architetturali, e così via.

> Tutto quello tra `<parentesi angolari>` da qui in poi è un segnaposto — sostituiscilo col tuo valore reale prima di eseguire il comando, non incollarlo così com'è.

**Fai i passi 2.1–2.6 da solo, senza Claude** — una sessione Claude Code nuova su un PC nuovo non può ancora leggere questo file (non è clonato, e questo repo è privato, quindi non c'è scorciatoia tipo "leggi il README su GitHub"). Una volta finito il passo 2.6 e il repo è clonato in locale, *allora* puoi aprire Claude Code in quella cartella e dire "leggi questo README e completa i passi rimanenti" — da lì in poi può aiutarti.

```bash
# 2.1 — genera una key dedicata a questo repo (mai riusare una key di un altro progetto/server)
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519_dotfiles -N '' -C 'claude-dotfiles-<nome-macchina>'
```

```bash
# 2.2 — aggiungi un host alias cosi git usa quella key solo per questo repo
cat >> ~/.ssh/config << 'EOF'

Host github-dotfiles
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_dotfiles
  IdentitiesOnly yes
EOF
chmod 600 ~/.ssh/config
```

```bash
# 2.3 — stampa la chiave pubblica, copiala
cat ~/.ssh/id_ed25519_dotfiles.pub
```

2.4 — Su GitHub: questo repo → **Settings → Deploy keys → Add deploy key** → incollala. Spunta **Allow write access** solo se pushi aggiornamenti da questa macchina, altrimenti lasciala di sola lettura.

```bash
# 2.5 — verifica che la key funzioni (l'errore "shell access" è atteso, conferma solo l'autenticazione)
ssh -T github-dotfiles
```

```bash
# 2.6 — clona e ripristina
git clone github-dotfiles:MikroT/claude-dotfiles.git
cp claude-dotfiles/CLAUDE.md ~/.claude/CLAUDE.md
```

Se `~/.claude/CLAUDE.md` ha già altro contenuto su questa macchina, unisci a mano invece di sovrascrivere.

**Checkpoint:** apri `claude` su una cartella vuota e chiedi qualcosa che dovrebbe attivare una regola (es. "crea un file X") — dovrebbe chiederti conferma prima di crearlo, e risponderti in italiano.

### 3. Riconnettiti a un progetto esistente (esempio: MCM)

Ogni repo di progetto ha bisogno della **propria** key dedicata — le deploy key sono per-repo, le chiavi non vanno mai condivise tra progetti.

**Stessa regola di sopra** — genera la key, aggiungila come Deploy Key, verificala, e clona, tutto da solo. Claude non può accedere a un repo privato per cui non ha una key. Una volta riuscito il clone, può proseguire dal `CLAUDE.md` e dai `docs/` del progetto stesso.

```bash
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519_mcm -N '' -C 'mcm-<nome-macchina>'

cat >> ~/.ssh/config << 'EOF'

Host github-mcm
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_mcm
  IdentitiesOnly yes
EOF

cat ~/.ssh/id_ed25519_mcm.pub
```

Aggiungila come Deploy Key (con write access se prevedi di pushare) sul repo GitHub del progetto, verifica con `ssh -T github-mcm`, poi:

```bash
git clone github-mcm:MikroT/mcm.git
```

Recuperi il codice **e** `docs/decisions/` (gli Architecture Decision Record del progetto) **e** `docs/runbooks/` (le sue procedure operative) **e** il suo `CLAUDE.md` a livello di progetto — tutto versionato, tutto ripristinato automaticamente dal clone.

Ripeti l'intero passaggio, con la sua key e il suo alias, per ogni altro repo di progetto su cui lavori.

**Progetto nuovo senza documentazione?** Usa [`docs-standard-setup-prompt.md`](docs-standard-setup-prompt.md) — incollalo nella sessione Claude Code di quel progetto per avviare lì lo standard di documentazione completo (ADR, runbook, e i pattern a trigger), inclusa la ricostruzione retroattiva da git history e documentazione esistente.

### 4. Se il progetto vive su un server remoto (esempio: il VPS di MCM)

Questo è separato dall'accesso GitHub — è l'accesso SSH al server stesso dove l'app gira davvero e viene deployata.

```bash
ssh-keygen -t ed25519 -f ~/.ssh/keys/<alias-server> -N ''
cat >> ~/.ssh/config << 'EOF'

Host <alias-server>
  HostName <ip-server>
  User <utente-server>
  Port <porta-server>
  IdentityFile ~/.ssh/keys/<alias-server>
EOF
```

Manda la chiave **pubblica** (`.pub`) a chi gestisce il server perché venga aggiunta al suo `~/.ssh/authorized_keys` — questo è manuale, non esiste una sincronizzazione automatica per l'accesso ai server.

### 5. Cosa NON torna automaticamente

Da gestire con attenzione — nessuno di questi è coperto dal clone di un repo:

| Cosa | Perché non si sincronizza | Cosa fare |
|---|---|---|
| Memoria per-progetto di Claude (`~/.claude/projects/*/memory/`) | Vive solo sulla macchina che l'ha scritta, non è in nessun repo | Fai backup manuale di quella cartella se la continuità ti serve, oppure lasciala ricostruire nel tempo sulla nuova macchina |
| Accesso SSH a server/VPS | Indipendente da GitHub, legato all'`authorized_keys` di quel server | Ripeti il passaggio 4 per ogni server, coordinandoti con chi lo amministra |
| API key (Gemini, Anthropic console, chiavi provider usate dall'app) | Deliberatamente mai salvate in nessun repo | Reinseriscile a mano nel `.env` / secrets manager dell'app al primo deploy dalla nuova macchina |
| `settings.json` (permission rule locali di Claude Code) | Piena di path specifici della macchina, non portabile | Si ricostruisce da sola man mano che approvi permessi sulla nuova macchina — non copiare quella vecchia |

### 6. Come mantenere aggiornato questo repo

Quando durante una sessione emerge una nuova regola/preferenza generale, va aggiunta a `~/.claude/CLAUDE.md` sulla macchina in uso, poi sincronizzata qui con un commit — stesso file, non un processo separato. Il `CLAUDE.md` globale stesso porta un promemoria permanente di proporre questa sincronizzazione ogni volta che una regola cambia, così non resta affidato alla memoria.

### 7. Indice progetti

[`projects-index.md`](projects-index.md) traccia ogni progetto su cui lavoro con Claude Code — scopo, stato, dove gira, link al repo. Si aggiorna come sottoprodotto del lavoro su quel progetto (il `CLAUDE.md` globale porta il promemoria), non come lavoro manuale separato o scansione continua.

### Cosa NON va in questo repo

Mai credenziali, `.credentials.json`, `settings.json`, `history.jsonl`, o la cartella `projects/` (memoria per-progetto, potenzialmente dati sensibili di clienti). Solo config comportamentale generica.
