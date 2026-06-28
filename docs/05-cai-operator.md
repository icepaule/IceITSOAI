# CAI-Operator & Netz-Isolation

Der CAI-Operator (Ubuntu 24.04 + CAI-Framework) führt autorisierte Aktionen **agentisch**
aus: Recon, Enumeration, Verprobung, Tool-Nachladen, Web-Recherche — mit
**Human-in-the-Loop** (Freigabe vor jedem Befehl) und vollständigem Tracing.

> **Basis-Image:** Ubuntu 24.04 (Python 3.12), **nicht** Kali. Das CAI-Framework wird offiziell
> auf Ubuntu unterstützt; rollende Kali-Mirror führten zu instabilen Builds. Benötigte Tools
> (nmap etc.) werden gezielt per `apt` installiert, weiteres bei Bedarf zur Laufzeit nachgeladen.

## Governance-Kontrollen
- **Scope/RoE** (`cai/scope.txt`): nur gelistete Ziele; der eigene Host ist out of scope.
- **HITL an** (`CAI_HITL=true`): Agent fragt vor jeder Ausführung.
- **Runaway-Schutz** (`CAI_MAX_TURNS`).
- **Tracing/Audit** (`CAI_TRACING=true`): Nachweis für BaFin/DORA.
- **Verbote**: DoS, PII-Exfiltration, unkoordinierte Persistenz.

## Playbooks (Operator)
```bash
docker compose exec cai-operator /operator/run-playbook.sh recon      <host>
docker compose exec cai-operator /operator/run-playbook.sh webapp     <url>
docker compose exec cai-operator /operator/run-playbook.sh extsurface <domain>
docker compose exec cai-operator /operator/run-playbook.sh intnet     <subnet>
docker compose exec cai-operator /operator/run-playbook.sh attackpath <host>
docker compose exec cai-operator /operator/run-playbook.sh tlpt       <system>
```

## Lokaler Threat-Intel-/Exploit-Korpus
Der Operator bindet den täglich aktualisierten Feed-Speicher **read-only** ein
(`/operator/threat-feeds`). Die Playbook-Objectives weisen den Agenten an, **zuerst** lokal
zu konsultieren statt blind ins Netz zu gehen, und gefundene PoCs **mit Quell-Pfad zu zitieren**:

- `indexes/` — High-Signal-Markdown (CISA KEV, EPSS-hoch, Metasploit-Module, ThreatFox-IOCs,
  OTX-Pulses, Threat-Intel-RSS), z. B. `grep -i <CVE|Produkt> /operator/threat-feeds/indexes/*.md`
- `indexes-disk/` — große Roh-Pointer-Indizes (Exploit-DB, Nuclei-CVE-Templates, PoC-in-GitHub)
- `raw/` — echter Exploit-/PoC-Code (`exploitdb/`, `nuclei-templates/`, `poc-in-github/`, `metasploit/modules/`)

Details zu Quellen und Refresh: [09 — Threat-Intel-Feeds](09-threat-intel-feeds.md).

## Netz-Isolation
**Default:** Bridge/Host-Routing — schnell, aber Traffic erscheint mit Host-IP.
**Empfohlen:** macvlan auf dediziertem Test-VLAN (eigene IP/MAC, kein Host-SNAT).

```mermaid
flowchart LR
  subgraph Host["GPU-Host"]
    OLL["Ollama"]
    SX["SearXNG"]
    CAI["CAI-Operator"]
  end
  CAI -- "ctrl-Netz<br/>LLM/Suche/NVD" --> OLL
  CAI -- "ctrl-Netz" --> SX
  CAI == "macvlan<br/>&lt;PARENT_IF&gt;.&lt;TEST_VLAN&gt;" ==> TGT["Test-Ziele"]
```

Aktivierung (Werte an eigene NIC/VLAN anpassen):
```bash
bash cai/network-setup.sh                 # legt VLAN-Subinterface + macvlan-Docker-Netz an
docker compose -f docker-compose.yml -f docker-compose.netiso.yml up -d
```

> macvlan-Container sind per Default nicht vom Host direkt erreichbar (by design);
> Steuerung/LLM/NVD laufen daher über das zusätzliche `ctrl`-Netz.
