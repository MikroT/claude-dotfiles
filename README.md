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
mkdir -p ~/.claude/skills/docs-workflow
cp claude-dotfiles/skills/docs-workflow/SKILL.md ~/.claude/skills/docs-workflow/SKILL.md
mkdir -p ~/.claude/agents
cp claude-dotfiles/agents/*.md ~/.claude/agents/
```

If `~/.claude/CLAUDE.md` already has other content on this machine, merge by hand instead of overwriting. The global `CLAUDE.md` points to the `docs-workflow` skill for ADRs/runbooks/session-notes/README-freshness/the knowledge graph — without that skill restored too, those rules won't apply on the new machine. The `agents/` copy restores custom global subagents (currently `code-reviewer` — an independent, read-only reviewer that checks code against the project's ADRs/runbooks); without it, `code-reviewer` won't be available on the new machine.

`settings-portable.json` carries the machine-independent behavioral preferences from `settings.json` — `model`, `effortLevel`, `switchModelsOnFlag`, `agentPushNotifEnabled`, `attribution`. **Merge these keys by hand** into the new machine's `~/.claude/settings.json` rather than copying the file over — that file will already have (or will grow) its own `permissions`, `hooks`, `statusLine`, and `enabledPlugins` for this machine, and a blind copy would clobber them. If you have `jq`, this does the merge for you (portable keys win, everything else in the local file is kept):

```bash
# 2.7 — merge the portable behavioral settings into the local settings.json
jq -s '.[0] * .[1]' ~/.claude/settings.json claude-dotfiles/settings-portable.json > /tmp/settings-merged.json \
  && mv /tmp/settings-merged.json ~/.claude/settings.json
```

Without `jq`, just open both files and copy the five keys in by hand — it's small and one-time. **`permissions.allow`, `hooks`, `statusLine`, and `enabledPlugins` (e.g. the caveman plugin) stay local to each machine on purpose** — they reference machine-specific paths or a plugin you may or may not reinstall here, and rebuild naturally as you use Claude Code and approve permissions day to day. Never copy them in bulk from another machine.

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
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519_<server-alias> -N ''
cat >> ~/.ssh/config << 'EOF'

Host <server-alias>
  HostName <server-ip>
  User <server-user>
  Port <server-port>
  IdentityFile ~/.ssh/id_ed25519_<server-alias>
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
| `settings.json`'s `permissions`, `hooks`, `statusLine`, `enabledPlugins` | Full of machine-specific paths, or a plugin choice tied to this machine | Rebuild organically as you approve permissions and (re)install hooks/plugins on the new machine — don't copy the old file over |
| `settings.json`'s behavioral preferences (`model`, `effortLevel`, `switchModelsOnFlag`, `agentPushNotifEnabled`, `attribution`) | These ARE portable | Already covered — merge `settings-portable.json` into the new machine's `settings.json` (step 2.7) |

### 6. Keeping this repo up to date

When a new general rule/preference comes up in any session, it gets added to `~/.claude/CLAUDE.md` on the machine in use, then synced here with a commit — same file, not a separate process. The global `CLAUDE.md` itself carries a standing reminder to offer this sync whenever a rule changes, so it's never left to memory. The `docs-workflow` skill (documentation rules — ADRs, runbooks, session notes, README/projects-index freshness, the knowledge graph) follows the same mechanism: a change there is proposed and synced to `skills/docs-workflow/SKILL.md` in this repo the same way, not treated separately. Custom global subagents under `~/.claude/agents/` (currently `code-reviewer`) are synced to `agents/` here the same way.

### 7. Projects index

[`projects-index.md`](projects-index.md) tracks every project worked on with Claude Code — purpose, status, where it runs, repo link. It's updated as a byproduct of working on a given project (the global `CLAUDE.md` carries the reminder), not by a separate manual chore or continuous scan.

### 8. How I use goal / workflow / loop with code-reviewer

The `code-reviewer` subagent ([`agents/code-reviewer.md`](agents/code-reviewer.md)) is read-only by design — it reports findings, it never edits. That makes it a clean *verifier* to drop into an iterative loop where a **different** role does the writing. Three ways I combine it:

**1. `/goal` + code-reviewer — one task with a clear "done" test.**

An implementer writes the change; `code-reviewer` checks it against the project's ADRs and re-runs the relevant checks; blocking findings go back to the implementer; repeat until the reviewer reports none.

```
/goal Implement <the task>. Loop: an implementer makes the change, then
the code-reviewer subagent (read-only) checks it against docs/decisions/
and runs <the project's verify/test command>. If code-reviewer returns
blocking findings, hand them back to the implementer to fix and
re-review. Done only when code-reviewer reports zero blocking findings.
Max 3 review cycles — then stop and report status even if not resolved.
```

**2. Dynamic workflow (`use a workflow`) + code-reviewer — big multi-step work (migrations, refactors).**

Orchestrator → implementer → code-reviewer (verifier) → a *separate* fixer → code-reviewer again. Key rule: `code-reviewer` stays read-only, and the fix is always made by a different role — never the same turn that judged the code.

```
Use a workflow for this. The orchestrator plans the steps. For each step:
an implementer subagent writes the code; then the code-reviewer subagent
(read-only) verifies it against the project's ADRs and runbooks; if it
returns blocking findings, a SEPARATE fixer subagent applies the fixes
(never the reviewer, never the same turn that judged); then code-reviewer
runs once more. code-reviewer stays read-only throughout. Cap each step at
3 review->fix cycles — if still not clean, the orchestrator records the
open findings and moves on, reporting them all at the end.
```

**3. `/loop` + code-reviewer — recurring automation (e.g. periodic review of open PRs).**

```
/loop 30m Review every open PR on this repo whose head changed since the
last run: for each, spawn the code-reviewer subagent to check the diff
against docs/decisions/ and docs/runbooks/, and post its blocking /
non-blocking findings as one summary comment. Do not fix anything. Skip a
PR already reviewed at its current head SHA. Cap at 5 PRs per run.
```

**Always cap the iterations.** Every one of these patterns must carry an explicit ceiling — e.g. "max 3 cycles, then stop and report the state even if unresolved". Without it, an implementer and a reviewer that don't converge keep handing work back and forth, burning a long run for no progress. The cap turns a non-converging loop into a bounded task that ends with an honest "here's what's still open".

### What never goes in this repo

Never credentials, `.credentials.json`, the full `settings.json` (its `permissions`/`hooks`/`statusLine`/`enabledPlugins` are machine-specific — only the curated `settings-portable.json` subset above is committed), `history.jsonl`, or the `projects/` folder (per-project memory, potentially sensitive client data). Only generic behavioral config.

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
mkdir -p ~/.claude/skills/docs-workflow
cp claude-dotfiles/skills/docs-workflow/SKILL.md ~/.claude/skills/docs-workflow/SKILL.md
mkdir -p ~/.claude/agents
cp claude-dotfiles/agents/*.md ~/.claude/agents/
```

Se `~/.claude/CLAUDE.md` ha già altro contenuto su questa macchina, unisci a mano invece di sovrascrivere. Il `CLAUDE.md` globale rimanda alla skill `docs-workflow` per ADR/runbook/session-notes/freshness del README/grafo di conoscenza — senza ripristinare anche quella skill, quelle regole non valgono sulla macchina nuova. La copia di `agents/` ripristina i subagent globali custom (per ora `code-reviewer` — un reviewer indipendente in sola lettura che confronta il codice con gli ADR/runbook del progetto); senza, `code-reviewer` non sarà disponibile sulla macchina nuova.

`settings-portable.json` porta le preferenze comportamentali indipendenti dalla macchina prese da `settings.json` — `model`, `effortLevel`, `switchModelsOnFlag`, `agentPushNotifEnabled`, `attribution`. **Unisci queste chiavi a mano** dentro il `~/.claude/settings.json` della macchina nuova invece di copiare il file intero — quel file avrà già (o si costruirà) i suoi `permissions`, `hooks`, `statusLine` ed `enabledPlugins` specifici per questa macchina, e una copia cieca li cancellerebbe. Se hai `jq`, questo comando fa il merge al posto tuo (le chiavi portabili vincono, tutto il resto del file locale resta):

```bash
# 2.7 — unisci le impostazioni comportamentali portabili al settings.json locale
jq -s '.[0] * .[1]' ~/.claude/settings.json claude-dotfiles/settings-portable.json > /tmp/settings-merged.json \
  && mv /tmp/settings-merged.json ~/.claude/settings.json
```

Senza `jq`, apri semplicemente i due file e copia le cinque chiavi a mano — è piccolo e una tantum. **`permissions.allow`, `hooks`, `statusLine` ed `enabledPlugins` (es. il plugin caveman) restano locali per macchina di proposito** — fanno riferimento a path specifici della macchina o a un plugin che potresti o meno reinstallare qui, e si ricostruiscono naturalmente man mano che usi Claude Code e approvi i permessi giorno per giorno. Non copiarli mai in blocco da un'altra macchina.

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
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519_<alias-server> -N ''
cat >> ~/.ssh/config << 'EOF'

Host <alias-server>
  HostName <ip-server>
  User <utente-server>
  Port <porta-server>
  IdentityFile ~/.ssh/id_ed25519_<alias-server>
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
| `permissions`, `hooks`, `statusLine`, `enabledPlugins` di `settings.json` | Pieni di path specifici della macchina, o una scelta di plugin legata a questa macchina | Si ricostruiscono da soli man mano che approvi i permessi e (re)installi hook/plugin sulla macchina nuova — non copiare il vecchio file intero |
| Le preferenze comportamentali di `settings.json` (`model`, `effortLevel`, `switchModelsOnFlag`, `agentPushNotifEnabled`, `attribution`) | Queste SONO portabili | Già coperto — unisci `settings-portable.json` nel `settings.json` della macchina nuova (passo 2.7) |

### 6. Come mantenere aggiornato questo repo

Quando durante una sessione emerge una nuova regola/preferenza generale, va aggiunta a `~/.claude/CLAUDE.md` sulla macchina in uso, poi sincronizzata qui con un commit — stesso file, non un processo separato. Il `CLAUDE.md` globale stesso porta un promemoria permanente di proporre questa sincronizzazione ogni volta che una regola cambia, così non resta affidato alla memoria. La skill `docs-workflow` (regole di documentazione — ADR, runbook, session notes, freshness di README/projects-index, grafo di conoscenza) segue lo stesso meccanismo: una modifica lì viene proposta e sincronizzata su `skills/docs-workflow/SKILL.md` in questo repo allo stesso modo, non trattata separatamente. I subagent globali custom sotto `~/.claude/agents/` (per ora `code-reviewer`) sono sincronizzati su `agents/` qui allo stesso modo.

### 7. Indice progetti

[`projects-index.md`](projects-index.md) traccia ogni progetto su cui lavoro con Claude Code — scopo, stato, dove gira, link al repo. Si aggiorna come sottoprodotto del lavoro su quel progetto (il `CLAUDE.md` globale porta il promemoria), non come lavoro manuale separato o scansione continua.

### 8. Come uso goal / workflow / loop con code-reviewer

Il subagent `code-reviewer` ([`agents/code-reviewer.md`](agents/code-reviewer.md)) è read-only per scelta — riporta i problemi, non modifica mai. Questo lo rende un *verifier* pulito da inserire in un loop iterativo dove a scrivere è un ruolo **diverso**. Tre modi in cui lo combino:

**1. `/goal` + code-reviewer — un singolo task con criterio di completamento chiaro.**

Un implementer scrive la modifica; `code-reviewer` la verifica contro gli ADR del progetto e rilancia i check pertinenti; i problemi bloccanti tornano all'implementer; si ripete finché il reviewer non ne trova più.

```
/goal Implementa <il task>. Loop: un implementer fa la modifica, poi il
subagent code-reviewer (read-only) la controlla contro docs/decisions/ e
lancia <il comando verify/test del progetto>. Se code-reviewer restituisce
problemi bloccanti, rimandali all'implementer per il fix e la re-review.
Fatto solo quando code-reviewer riporta zero problemi bloccanti. Massimo
3 cicli di review — poi fermati e riporta lo stato anche se non risolto.
```

**2. Dynamic workflow (`use a workflow`) + code-reviewer — task grandi multi-step (migrazioni, refactor).**

Orchestratore → implementer → code-reviewer (verifier) → un fixer *separato* → di nuovo code-reviewer. Regola chiave: `code-reviewer` resta read-only, e il fix lo fa sempre un ruolo diverso — mai lo stesso turno che ha giudicato il codice.

```
Usa un workflow per questo. L'orchestratore pianifica gli step. Per ogni
step: un subagent implementer scrive il codice; poi il subagent
code-reviewer (read-only) lo verifica contro gli ADR e i runbook del
progetto; se restituisce problemi bloccanti, un subagent fixer SEPARATO
applica le correzioni (mai il reviewer, mai lo stesso turno che ha
giudicato); poi code-reviewer rilancia una volta. code-reviewer resta
read-only per tutto. Metti un tetto di 3 cicli review->fix per step — se
non è ancora pulito, l'orchestratore registra i problemi aperti e va
avanti, riportandoli tutti alla fine.
```

**3. `/loop` + code-reviewer — automazione ricorrente (es. review periodica delle PR aperte).**

```
/loop 30m Rivedi ogni PR aperta su questo repo il cui head è cambiato
dall'ultimo giro: per ciascuna, lancia il subagent code-reviewer per
controllare il diff contro docs/decisions/ e docs/runbooks/, e posta i
suoi problemi bloccanti / non bloccanti come un unico commento riassuntivo.
Non correggere niente. Salta una PR già rivista al suo head SHA corrente.
Tetto di 5 PR per giro.
```

**Metti sempre un tetto alle iterazioni.** Ognuno di questi pattern deve avere un limite esplicito — es. "massimo 3 cicli, poi fermati e riporta lo stato anche se non risolto". Senza, un implementer e un reviewer che non convergono continuano a rimbalzarsi il lavoro, bruciando un giro lungo senza progresso. Il tetto trasforma un loop che non converge in un task limitato che finisce con un onesto "ecco cosa resta aperto".

### Cosa NON va in questo repo

Mai credenziali, `.credentials.json`, il `settings.json` intero (`permissions`/`hooks`/`statusLine`/`enabledPlugins` sono specifici della macchina — è committato solo il sottoinsieme curato `settings-portable.json` sopra), `history.jsonl`, o la cartella `projects/` (memoria per-progetto, potenzialmente dati sensibili di clienti). Solo config comportamentale generica.
