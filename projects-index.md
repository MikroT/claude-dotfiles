# Projects Index

[English](#english) · [Italiano](#italiano)

---

## English

A running index of projects worked on with Claude Code — not a replacement for GitHub's own list, but the context GitHub doesn't show: purpose, current state, where it actually runs, and a link to its repo (from which its `docs/decisions/` and `docs/runbooks/` are reachable).

**How this stays up to date:** updated during sessions that touch a given project, not by continuous scanning — see the rule in [`CLAUDE.md`](CLAUDE.md). If a project hasn't been touched in a session in a while, its entry may be stale; treat "last updated" as a freshness signal.

| Project | Purpose | Status | Repo | Runs on | Last updated |
|---|---|---|---|---|---|
| MCM (Auriessence) | Management dashboard for Auriessence (fragrances/candles e-commerce): orders, inventory, invoicing, Meta Catalog sync | Active | [MikroT/mcm](https://github.com/MikroT/mcm) | Hetzner VPS (Sicuraweb), Docker Compose | 2026-08-01 |
| awra | Convergence of MCM and Aura into one AI-agent-driven business platform: ERPNext (Frappe) as the data/backend engine, Agno as the multi-model agent framework — replaces both projects' custom-built ERP-adjacent functionality | Active (pre-alpha — 26 ADRs; Phase 1 complete, Phase 2 in progress. Phase 1 runs against a real tenant: tenant provisioning, the Agno agent with live ERPNext tools under per-user credentials, and tracing to SigNoz, all real not spikes. Phase 2 so far: agent memory and the per-tenant pgvector RAG knowledge base wired into the agent (ADR 0026), with fail-closed isolation tests extended to memory/RAG (mutation-tested against real Postgres); AgentOS JWT HTTP endpoint as the real-client validation entry point. Live WhatsApp round trip still deliberately deferred pending a scope decision) | [MikroT/awra](https://github.com/MikroT/awra) | Local (WSL2) for now — production hosting undecided | 2026-08-28 |
| synology-backup-reports | Weekly per-client backup status emails built on top of Synology Active Backup for Business, whose own reporting is NAS-wide and not client-facing | Active | [MikroT/synology-backup-reports](https://github.com/MikroT/synology-backup-reports) | Synology NAS itself, via DSM Task Scheduler | 2026-08-07 |
| claude-dotfiles | This repo — personal Claude Code config, onboarding guide, documentation standards | Active | [MikroT/claude-dotfiles](https://github.com/MikroT/claude-dotfiles) | N/A — config only, doesn't run anywhere | 2026-08-01 |
| proxmox-infrastructure | On-premise Proxmox VE cluster replacing the sicuraweb and TacticalRMM Hetzner VPS instances, growing into a multi-node cluster over time — will also host AWRA's production deployment eventually | Active (TacticalRMM fully migrated and live — 148 agents/39 clients; Infisical live for machine secrets, full disaster-recovery backup taken; UrBackup/Infscape client backup running for 4 real client PCs; old Hetzner TacticalRMM VPS decommissioned; `pdm-host` build in progress — install not yet confirmed complete; a router-side ARP fault has twice knocked a VM off its gateway, unresolved, needs a physical router power-cycle) | [MikroT/proxmox-infrastructure](https://github.com/MikroT/proxmox-infrastructure) | Owner-owned physical hardware (Xeon appliances), accessed via Tailscale | 2026-08-23 |
| knowledge-graphify | Cross-project knowledge graph over active repos (awra, mcm, synology-backup-reports, proxmox-infrastructure), built with graphify and synced across machines via git — a derived, regenerable index answering cross-project questions, not a replacement for each project's own ADRs/runbooks | Active | [MikroT/knowledge-graphify](https://github.com/MikroT/knowledge-graphify) | N/A — no live service, just a git-synced graph file | 2026-08-28 |

**Status values:** `Active` (worked on regularly) · `Stable` (done, not actively changed, still in use) · `Paused` (intentionally on hold) · `Archived` (no longer maintained).

---

## Italiano

Un indice vivo dei progetti su cui lavoro con Claude Code — non sostituisce la lista GitHub, ma aggiunge il contesto che GitHub non mostra: scopo, stato attuale, dove gira davvero, e un link al suo repo (da cui si raggiungono `docs/decisions/` e `docs/runbooks/`).

**Come resta aggiornato:** aggiornato durante le sessioni che toccano un dato progetto, non da scansione continua — vedi la regola in [`CLAUDE.md`](CLAUDE.md). Se un progetto non viene toccato in sessione da un po', la sua entry potrebbe essere non aggiornata; tratta "ultimo aggiornamento" come segnale di freschezza, non garanzia.

| Progetto | Scopo | Stato | Repo | Gira su | Ultimo aggiornamento |
|---|---|---|---|---|---|
| MCM (Auriessence) | Dashboard gestionale per Auriessence (e-commerce profumi/candele): ordini, inventario, fatturazione, sync Meta Catalog | Attivo | [MikroT/mcm](https://github.com/MikroT/mcm) | VPS Hetzner (Sicuraweb), Docker Compose | 2026-08-01 |
| awra | Convergenza di MCM e Aura in un'unica piattaforma business guidata da agente AI: ERPNext (Frappe) come motore dati/backend, Agno come framework agente multi-model — sostituisce le funzionalità ERP-simili costruite da zero in entrambi i progetti | Attivo (pre-alpha — 26 ADR; Fase 1 completa, Fase 2 in corso. La Fase 1 gira su un tenant reale: provisioning tenant, agente Agno con tool ERPNext live sotto credenziali per-utente, e tracing su SigNoz, tutti reali non spike. Fase 2 finora: memoria dell'agente e knowledge base RAG pgvector per-tenant integrate nell'agente (ADR 0026), con test di isolamento fail-closed estesi a memoria/RAG (mutation-tested su Postgres reale); endpoint HTTP JWT di AgentOS come punto di ingresso per la validazione con cliente reale. Round trip WhatsApp live ancora volutamente rimandato in attesa di una decisione di scope) | [MikroT/awra](https://github.com/MikroT/awra) | Locale (WSL2) per ora — hosting produzione non deciso | 2026-08-28 |
| synology-backup-reports | Report settimanali per cliente sullo stato backup, costruiti sopra Synology Active Backup for Business (il cui reporting nativo è per l'intero NAS, non per singolo cliente) | Attivo | [MikroT/synology-backup-reports](https://github.com/MikroT/synology-backup-reports) | NAS Synology stesso, via DSM Task Scheduler | 2026-08-07 |
| claude-dotfiles | Questo repo — config personale Claude Code, guida onboarding, standard di documentazione | Attivo | [MikroT/claude-dotfiles](https://github.com/MikroT/claude-dotfiles) | N/D — solo config, non gira da nessuna parte | 2026-08-01 |
| proxmox-infrastructure | Cluster Proxmox VE on-premise che sostituisce i VPS Hetzner di sicuraweb e TacticalRMM, pensato per crescere a cluster multi-nodo nel tempo — ospiterà anche il deploy di produzione di AWRA in futuro | Attivo (TacticalRMM completamente migrato e live — 148 agenti/39 client; Infisical live per secret machine-readable, backup disaster-recovery completo eseguito; UrBackup/Infscape in produzione per 4 client PC reali; vecchio VPS Hetzner TacticalRMM dismesso; build di `pdm-host` in corso — installazione non ancora confermata completa; un guasto ARP lato router ha isolato una VM dal gateway due volte, irrisolto, serve power-cycle fisico del router) | [MikroT/proxmox-infrastructure](https://github.com/MikroT/proxmox-infrastructure) | Hardware fisico di proprietà (appliance Xeon), accesso via Tailscale | 2026-08-23 |
| knowledge-graphify | Grafo di conoscenza cross-progetto sui repo attivi (awra, mcm, synology-backup-reports, proxmox-infrastructure), costruito con graphify e sincronizzato tra macchine via git — un indice derivato e rigenerabile che risponde a domande cross-progetto, non sostituisce gli ADR/runbook di ciascun progetto | Attivo | [MikroT/knowledge-graphify](https://github.com/MikroT/knowledge-graphify) | N/D — nessun servizio live, solo un file grafo sincronizzato via git | 2026-08-28 |

**Valori di stato:** `Attivo` (ci lavori regolarmente) · `Stabile` (finito, non modificato attivamente, ancora in uso) · `In pausa` (fermo intenzionalmente) · `Archiviato` (non più mantenuto).
