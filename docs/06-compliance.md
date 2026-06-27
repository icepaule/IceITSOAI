# Compliance-Mapping (DORA / TIBER-EU / BaFin / EZB / MITRE)

Der System-Prompt erzwingt für jeden Befund ein festes Schema. Damit sind Ergebnisse
prüfbar, vergleichbar und revisionssicher.

## Ausgabeschema
1. **Asset / Scope** — System, IP, Owner, Kritikalität (C-I-A), betroffene kritische Funktion.
2. **Threat Scenario** — Threat-Actor + Motivation (TIBER-Threat-Intel-Stil).
3. **Attack Path** — Kill-Chain, jeder Schritt mit **MITRE ATT&CK** Tactic + Technique-ID.
4. **Proof of Concept** — reproduzierbar, Labor-Scope, validierungspflichtig.
5. **Impact** — technisch + geschäftlich.
6. **Regulatory Mapping** — DORA / TIBER-EU / BaFin / EZB (siehe unten).
7. **Remediation** — Fix + kompensierende Kontrolle + Priorität.
8. **Residual Risk** — Restrisiko nach Maßnahme.

## Regulatorische Bezüge
| Rahmen | Bezug im Befund |
|---|---|
| **DORA** | Art. 5–15 (ICT-Risk-Management), Art. 9 (Schutz/Prävention), Art. 24–27 (Testing/TLPT) |
| **TIBER-EU** | Phase: Preparation / Threat Intelligence / Red Team / Closure; TTPs gegen MITRE ATT&CK gemappt |
| **BaFin** | BAIT, MaRisk AT 7.2 / 7.3 |
| **EZB** | Cyber-Resilience-Erwartungen / SSM-Bezug (signifikante Institute) |
| **MITRE ATT&CK** | Tactic + Technique-ID je Attack-Path-Schritt (Quelle: RAG-STIX) |

## Datenquellen der Anreicherung
- **MITRE ATT&CK** (STIX) — TTP-/Technique-Mapping aus der RAG-Wissensbasis.
- **NIST NVD** — automatische CVE-Anreicherung (CVSS, CWE, Referenzen), siehe
  [NVD-Anreicherung](08-nvd-enrichment.md).
- **Web-Recherche** (SearXNG) — aktuelle Kontextinformationen.

## Abgrenzung
Werkzeug für internes Testing/Scoping/Reporting — **kein** akkreditiertes DORA-TLPT.
Formales TLPT erfordert externe, akkreditierte Tester + Control-/White-Team.
