# Architektur

## Komponenten
| Dienst | Rolle | Port |
|---|---|---|
| Ollama (Host) | LLM-Inferenz (Multi-GPU) | 11434 |
| Open WebUI | Frontend: Chat, RAG, Playbook-Sektion | 8080 |
| Qdrant | Vektor-DB für RAG-Wissensbasis | 6333 |
| SearXNG | self-hosted Web-Recherche | 8888 |
| CAI-Operator | agentische Ausführung (Kali + CAI) | — |

## Datenfluss (Befund-Erzeugung)

```mermaid
sequenceDiagram
    actor A as Analyst
    participant W as Open WebUI
    participant O as Ollama (secanalyst)
    participant Q as Qdrant (RAG)
    participant S as SearXNG
    A->>W: Playbook /pb-* + Ziel
    W->>Q: relevante MITRE/DORA-Passagen
    W->>S: optionale Web-Recherche (CVE/TTP)
    W->>O: Prompt + Kontext
    O-->>W: Befund im Compliance-Schema
    W-->>A: Threat → Attack-Path (MITRE) → PoC → DORA/TIBER/BaFin/EZB
```

## Agentische Ausführung (Operator)

```mermaid
sequenceDiagram
    actor A as Analyst
    participant C as CAI-Operator
    participant O as Ollama
    participant T as Test-Ziel (Scope)
    A->>C: Playbook + Ziel (run-playbook.sh)
    C->>O: Plan/Schritt erzeugen
    C->>A: HITL — Befehl + Risiko zur Freigabe
    A-->>C: Freigabe
    C->>T: Befehl ausführen (im Scope)
    T-->>C: Ergebnis
    C->>O: Ergebnis deuten → nächster Schritt
    C-->>A: Befund + Trace (auditiert)
```

## Netz-Sicht (mit optionaler L2-Isolation)

```mermaid
flowchart LR
  subgraph Host["GPU-Host"]
    OLL["Ollama"]
    WUI["Open WebUI"]
    SX["SearXNG"]
    QD["Qdrant"]
    CAI["CAI-Operator"]
  end
  CAI -- "ctrl-Netz" --> OLL
  CAI -- "ctrl-Netz" --> SX
  CAI == "macvlan (optional)<br/>Test-VLAN" ==> TGT["autorisierte Ziele"]
  WUI --> OLL
  WUI --> QD
  WUI --> SX
```

> Screenshots der laufenden Oberfläche: siehe [docs/img](img/). Platzhalter, bis
> sanitierte Screenshots (ohne interne IPs/Hostnamen) ergänzt werden.
