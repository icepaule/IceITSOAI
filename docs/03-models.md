# Modell-Stack & Abliteration

Auslegung für ~24 GB VRAM: Sweet Spot sind 30–34B-Modelle @ Q4 (~18–20 GB).

| Rolle | Modell | Zweck |
|---|---|---|
| Security-Primär | WhiteRabbitNeo V3 (7B) | SecOps-getunt, Exploit-PoC + Remediation |
| Reasoning/Unrestricted | Qwen3-32B *abliterated* | ungefiltertes Reasoning, wenn Modelle grundlos verweigern |
| Code/PoC | Qwen2.5-Coder-32B | sauberer Exploit-/Tooling-Code, robustes Tool-Calling |
| Embeddings (RAG) | bge-m3 | Wissensbasis-Vektorisierung |

## Abgeleitete SecAnalyst-Modelle (mit fest verdrahtetem System-Prompt)
- `secanalyst:dora` — erzwingt das Compliance-Ausgabeschema (Reporting).
- `secanalyst:operator` — agentisches Planungs-/Operator-Reasoning.
- `secanalyst:uncensored` — auf dem abliterierten 32B.

## Abliteration („unrestricted" sauber lösen)
*Abliteration* entfernt die „Refusal-Direction" aus den Modell-Aktivierungen — gleiche
Fähigkeit, keine grundlosen Verweigerungen. Werkzeug: **Heretic** (Optuna-getunt).

> **Hardware-Realität:** Ein 32B in FP16 (~64 GB) passt nicht in 24 GB. Optionen:
> - **Empfohlen (lokal):** vor-abliteriertes GGUF ziehen und als `qwen3-32b-abliterated`
>   registrieren (Sekunden, kein Training).
> - **Eigene Abliteration:** auf größerer GPU-Box (≥ 80 GB) ausführen, Ergebnis als
>   quantisiertes GGUF (Q4_K_M) auf den Host holen.
> - **Kleineres Ziel:** 14B lokal abliterieren.

## Qualitätshinweis
Uncensored-/abliterierte Modelle **halluzinieren** CVEs/Technique-IDs/PoCs. Der
System-Prompt erzwingt `[UNVERIFIZIERT]`-Kennzeichnung; jede technische Aussage und
jeder PoC ist vor Einsatz zu validieren. MITRE-IDs kommen aus der RAG-Wissensbasis
(STIX), nicht aus dem Modellgedächtnis.

## Alternativen-Bewertung (Cloud / geschlossene Programme)
| Option | Souveränität | Offensive-Eignung | DORA |
|---|---|---|---|
| Self-host (hier) | voll, air-gappable | unrestricted | kein Drittdienstleister |
| Kommerzielles On-Prem-Security-LLM | on-prem möglich | sehr stark | Drittpartei (Vertrag) |
| EU-Managed (souverän) | EU-Datenresidenz | guardrailed | EU-Drittpartei |
| US-Cloud / geschlossene Trusted-Access-Programme | US | stark, aber Zugang/Export beschränkt | US-Drittpartei, Konzentrationsrisiko |

Fazit: Für eine EU-Bank ist self-hosted die souveräne und regulatorisch sauberste Wahl.
