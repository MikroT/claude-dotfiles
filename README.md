# claude-dotfiles

Config personale globale per Claude Code — regole valide per ogni progetto su cui lavoro (non specifiche di un singolo repo).

## Ripristino su un nuovo PC

```
cp CLAUDE.md ~/.claude/CLAUDE.md
```

Se `~/.claude/CLAUDE.md` esiste già con altro contenuto, unire a mano invece di sovrascrivere.

## Cosa NON va in questo repo

Mai credenziali, `.credentials.json`, `settings.json` (contiene permission rule legate a path locali specifici, non portabili), `history.jsonl`, o la cartella `projects/` (contiene memoria specifica di singoli progetti, potenzialmente dati sensibili di clienti). Solo config comportamentale generica.
