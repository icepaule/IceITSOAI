# Setup & Deployment

Alle Platzhalter (`<KI02_HOST>`, Ports, VLANs) an die eigene Umgebung anpassen.

## Voraussetzungen
- GPU-Host mit Ollama (Multi-GPU empfohlen; ~24 GB VRAM ⇒ Modelle ≤ ~34B @ Q4).
- Docker + Docker Compose.
- Isoliertes Test-Netz/-VLAN für scharfe Engagements (siehe [05](05-cai-operator.md)).

## Reihenfolge
```bash
cp .env.example .env && $EDITOR .env        # Secrets/Ports/Host setzen

# 1) Modelle + Compliance-Modelfiles (secanalyst:dora / :operator)
bash bootstrap.sh

# 2) Eigenes abliteriertes 32B (siehe docs/03-models.md, Hardware-Hinweis!)
bash abliteration/heretic-run.sh
bash bootstrap.sh                            # erstellt jetzt secanalyst:uncensored

# 3) RAG-Wissensbasis (MITRE ATT&CK + Reg-Doku)
bash rag/load-knowledge.sh

# 4) Stack starten
docker compose up -d                         # WebUI :8080, SearXNG :8888, Qdrant, CAI
#    -> http://<KI02_HOST>:8080  (erster User = Admin, danach Signup zu)

# 5) Open-WebUI-Sektion (Playbooks) provisionieren
export WEBUI_TOKEN='...'                      # Admin-API-Key aus Open WebUI
bash openwebui/provision.sh

# 6) Agentischer Operator (autonom, HITL)
$EDITOR cai/scope.txt                         # Rules of Engagement PFLEGEN (Pflicht!)
docker compose exec cai-operator cai
```

## Verifikation
- `ollama list` zeigt `secanalyst:dora`, `secanalyst:operator`, ggf. `:uncensored`.
- `http://<KI02_HOST>:8080` erreichbar, Modelle wählbar.
- Im Chat `/pb-` zeigt die Playbooks.
- Web-Suche: SearXNG unter `http://<KI02_HOST>:8888` liefert JSON (`&format=json`).

## Ports
| Dienst | Default |
|---|---|
| Open WebUI | 8080 |
| SearXNG | 8888 |
| Qdrant | 6333 |
| Ollama (Host) | 11434 |
