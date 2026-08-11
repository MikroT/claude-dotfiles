# Projects Index

[English](#english) · [Italiano](#italiano)

---

## English

A running index of projects worked on with Claude Code — not a replacement for GitHub's own list, but the context GitHub doesn't show: purpose, current state, where it actually runs, and a link to its repo (from which its `docs/decisions/` and `docs/runbooks/` are reachable).

**How this stays up to date:** updated during sessions that touch a given project, not by continuous scanning — see the rule in [`CLAUDE.md`](CLAUDE.md). If a project hasn't been touched in a session in a while, its entry may be stale; treat "last updated" as a freshness signal.

| Project | Purpose | Status | Repo | Runs on | Last updated |
|---|---|---|---|---|---|
| MCM (Auriessence) | Management dashboard for Auriessence (fragrances/candles e-commerce): orders, inventory, invoicing, Meta Catalog sync | Active | [MikroT/mcm](https://github.com/MikroT/mcm) | Hetzner VPS (Sicuraweb), Docker Compose | 2026-08-01 |
| awra | Convergence of MCM and Aura into one AI-agent-driven business platform: ERPNext (Frappe) as the data/backend engine, Agno as the multi-model agent framework — replaces both projects' custom-built ERP-adjacent functionality | Active (pre-alpha, architecture design) | [MikroT/awra](https://github.com/MikroT/awra) | Local (WSL2) for now — production hosting undecided | 2026-08-07 |
| synology-backup-reports | Weekly per-client backup status emails built on top of Synology Active Backup for Business, whose own reporting is NAS-wide and not client-facing | Active | [MikroT/synology-backup-reports](https://github.com/MikroT/synology-backup-reports) | Synology NAS itself, via DSM Task Scheduler | 2026-08-07 |
| claude-dotfiles | This repo — personal Claude Code config, onboarding guide, documentation standards | Active | [MikroT/claude-dotfiles](https://github.com/MikroT/claude-dotfiles) | N/A — config only, doesn't run anywhere | 2026-08-01 |
| proxmox-infrastructure | On-premise Proxmox VE cluster replacing the sicuraweb and TacticalRMM Hetzner VPS instances, growing into a multi-node cluster over time — will also host AWRA's production deployment eventually | Active (first node up, first VM created, service migration not yet started) | [MikroT/proxmox-infrastructure](https://github.com/MikroT/proxmox-infrastructure) | Owner-owned physical hardware (Xeon appliances), accessed via Tailscale | 2026-08-11 |

**Status values:** `Active` (worked on regularly) · `Stable` (done, not actively changed, still in use) · `Paused` (intentionally on hold) · `Archived` (no longer maintained).

---

## Italiano

Un indice vivo dei progetti su cui lavoro con Claude Code — non sostituisce la lista GitHub, ma aggiunge il contesto che GitHub non mostra: scopo, stato attuale, dove gira davvero, e un link al suo repo (da cui si raggiungono `docs/decisions/` e `docs/runbooks/`).

**Come resta aggiornato:** aggiornato durante le sessioni che toccano un dato progetto, non da scansione continua — vedi la regola in [`CLAUDE.md`](CLAUDE.md). Se un progetto non viene toccato in sessione da un po', la sua entry potrebbe essere non aggiornata; tratta "ultimo aggiornamento" come segnale di freschezza, non garanzia.

| Progetto | Scopo | Stato | Repo | Gira su | Ultimo aggiornamento |
|---|---|---|---|---|---|
| MCM (Auriessence) | Dashboard gestionale per Auriessence (e-commerce profumi/candele): ordini, inventario, fatturazione, sync Meta Catalog | Attivo | [MikroT/mcm](https://github.com/MikroT/mcm) | VPS Hetzner (Sicuraweb), Docker Compose | 2026-08-01 |
| awra | Convergenza di MCM e Aura in un'unica piattaforma business guidata da agente AI: ERPNext (Frappe) come motore dati/backend, Agno come framework agente multi-model — sostituisce le funzionalità ERP-simili costruite da zero in entrambi i progetti | Attivo (pre-alpha, design architetturale) | [MikroT/awra](https://github.com/MikroT/awra) | Locale (WSL2) per ora — hosting produzione non deciso | 2026-08-07 |
| synology-backup-reports | Report settimanali per cliente sullo stato backup, costruiti sopra Synology Active Backup for Business (il cui reporting nativo è per l'intero NAS, non per singolo cliente) | Attivo | [MikroT/synology-backup-reports](https://github.com/MikroT/synology-backup-reports) | NAS Synology stesso, via DSM Task Scheduler | 2026-08-07 |
| claude-dotfiles | Questo repo — config personale Claude Code, guida onboarding, standard di documentazione | Attivo | [MikroT/claude-dotfiles](https://github.com/MikroT/claude-dotfiles) | N/D — solo config, non gira da nessuna parte | 2026-08-01 |
| proxmox-infrastructure | Cluster Proxmox VE on-premise che sostituisce i VPS Hetzner di sicuraweb e TacticalRMM, pensato per crescere a cluster multi-nodo nel tempo — ospiterà anche il deploy di produzione di AWRA in futuro | Attivo (primo nodo su, prima VM creata, migrazione servizi non ancora iniziata) | [MikroT/proxmox-infrastructure](https://github.com/MikroT/proxmox-infrastructure) | Hardware fisico di proprietà (appliance Xeon), accesso via Tailscale | 2026-08-11 |

**Valori di stato:** `Attivo` (ci lavori regolarmente) · `Stabile` (finito, non modificato attivamente, ancora in uso) · `In pausa` (fermo intenzionalmente) · `Archiviato` (non più mantenuto).
