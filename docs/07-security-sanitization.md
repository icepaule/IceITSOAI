# Security & Sanitization

Diese öffentliche Dokumentation darf **keine** operativen oder sensiblen Daten enthalten.

## Niemals veröffentlichen
- Interne IP-Adressen, Hostnamen, VLAN-IDs, reale Netz-Topologie.
- Zugangsdaten, API-Keys, Tokens, private Schlüssel.
- `.env`, `scope.txt` (Rules of Engagement mit echten Zielen).
- Engagement-Artefakte: Traces, Logs, Scan-Ergebnisse, nachgeladene Tools.
- RAG-Inhalte mit internen/lizenzierten Dokumenten.
- Bank-/Mandanten-Namen, PII, Befunde realer Systeme.

## Schutzmechanismen im Repo
- `.gitignore` blockt `.env`, `scope.txt`, `workspace/`, `traces/`, `knowledge/`, Keys, `*.gguf`.
- Platzhalter statt echter Werte: `<KI02_HOST>`, `<TEST_VLAN>`, `<TEST_SUBNET>`.
- Screenshots vor Upload schwärzen (IPs/Hostnamen/Befunde).

## Sanitization-Checkliste (vor jedem Push)
- [ ] Keine internen IPs/Hostnamen (grep nach `10.`, `192.168.`, internen Domains).
- [ ] Keine Secrets/Tokens/Keys (`grep -ri 'api[_-]key\|secret\|token\|password'`).
- [ ] Keine echten Scope-/Engagement-Daten.
- [ ] Screenshots geschwärzt.
- [ ] `git diff` vor Commit gesichtet.

## Rechtlicher Rahmen
Nutzung ausschließlich für **autorisiertes, rechtskonformes** Security-Testing eigener
bzw. ausdrücklich beauftragter Systeme, mit dokumentierten Rules of Engagement.
Uncensored-/abliterierte Modelle erzeugen ggf. falsche CVEs/PoCs — **Validierungspflicht**.
