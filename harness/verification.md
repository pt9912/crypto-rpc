# Verification Evidence

Diese Datei definiert, wie `crypto-rpc` Slice-Ergebnisse verifiziert.
Verification beantwortet: Wurde der Slice richtig gebaut, gemessen an
Lastenheft, Spezifikation, ADR, DoD und ausgefuehrten Sensors?

Validation ist getrennt: Sie fragt, ob das Ergebnis den realen Nutzer-,
Betreiber- oder Release-Bedarf trifft. Siehe [`harness/roles.md`](roles.md).

## Pflicht bei Slice-Closure

Jeder Slice, der nach `docs/plan/planning/done/` bewegt wird, braucht
eine sichtbare Verification-Evidence. Sie kann direkt im Slice stehen.
Nur bei sehr grosser Evidence entsteht ein eigenes Artefakt, das im
Slice verlinkt wird.

Minimum:

| Feld | Pflicht | Inhalt |
| --- | --- | --- |
| Scope | ja | Slice-ID, betroffene `RPC-*`-/`ADR-*`-IDs, relevante Dateien |
| DoD-Abgleich | ja | Welche DoD-Punkte sind erfuellt, welche nicht |
| Sensors | ja | Ausgefuehrte Pruefungen mit Ergebnis; aktuell oft manual |
| Traceability | ja | Welche Evidence belegt welche `RPC-*`- oder ADR-ID |
| Domaenenwirkung | ja | PKCS#11, Cloud-KMS, Netzwerk-HSM, Cloud-HSM: betroffen oder bewusst nicht |
| Nicht ausgefuehrt | ja | Ausgelassene oder nicht vorhandene Sensors mit Grund |
| Commit / Artefakt | wenn vorhanden | Commit-Hash, Release-Asset, Image-Tag, generiertes Artefakt |

## Evidence Block

Standardblock fuer Slice-Closure:

```markdown
## Verification Evidence

Scope:
- Slice: `<slice-id>`
- IDs: `<RPC-...>`, `<ADR-...>`
- Domaenen: `<PKCS#11|Cloud-KMS|Netzwerk-HSM|Cloud-HSM|cross-domain>`
- Artefakte: `<Dateien/Pakete/Commands>`

DoD-Abgleich:
- [x] `<DoD-Punkt>` - Evidence: `<Test/Gate/Diff/Review>`
- [ ] `<DoD-Punkt>` - offen: `<Grund/Folge-Slice>`

Sensors:
| Sensor | Ergebnis | Evidence |
| --- | --- | --- |
| `manual source-precedence` | pass/fail/not run | `<kurzer Beleg>` |
| `manual traceability` | pass/fail/not run | `<kurzer Beleg>` |
| `manual link/path check` | pass/fail/not run | `<kurzer Beleg>` |
| `make gates` | not available/pass/fail/not run | `<kurzer Beleg>` |

Traceability:
| ID | Beleg |
| --- | --- |
| `<RPC-...>` | `<Test/Gate/Doku>` |
| `<ADR-...>` | `<depguard/Test/Review/Doku>` |

Domaenenwirkung:
- PKCS#11: `<betroffen|nicht betroffen + Grund>`
- Cloud-KMS: `<betroffen|nicht betroffen + Grund>`
- Netzwerk-HSM: `<betroffen|nicht betroffen + Grund>`
- Cloud-HSM: `<betroffen|nicht betroffen + Grund>`

Nicht ausgefuehrt:
- `<Sensor>` - `<Grund>`

Commit / Artefakt:
- `<hash|image-tag|release-asset|n/a>`
```

## Sensor-Auswahl

Der Verifier waehlt den engsten sinnvollen Sensor, muss aber begruenden,
wenn ein naheliegender Sensor nicht gelaufen ist.

| Aenderung | Mindest-Pruefung heute | Normaler Sensor nach Build-Harness |
| --- | --- | --- |
| Nur Markdown/Doku | Source-Precedence, Traceability, Link-/Pfadpruefung | `make docs-check`, sobald vorhanden |
| Lastenheft/Spezifikation | ID-Schema, Konflikt mit `RPC-LESE-004`, Belegtyp-Pruefung | docs-check plus Review-/Verifier-Skill |
| ADR | ADR-Header, Index-Update, keine Lastenheft-Einschraenkung | docs-check plus ADR-Fitness-Sensor, sobald vorhanden |
| Planning-Lifecycle | Pfad, Status, Akzeptanzkriterien, Verifikationspfad | docs-check plus Closure-Sensor, sobald vorhanden |
| Go-/Generator-Code | nicht anwendbar, solange kein Code existiert | `make test` und `make gates` |
| Architektur-/Importregeln | nicht anwendbar, solange kein Code existiert | `make lint`/Architektur-Sensor und `make gates` |
| Proto-/Stub-/Runtime-Output | Golden-Plan und Proto-Pfad-ADR pruefen | `make proto-check`, Stub-Builds, `make test` |
| Docker-/Runtime-Pfad | `open/001` gegen Scope pruefen | `make ci` oder `make fullbuild` |
| Security-/Release-Pfad | Lastenheft-Belegtyp und Scope pruefen | Security-Sensors, Image-Scan, Release-Checklist |

## Harte Regeln

- Gruene Tests ersetzen nicht den DoD-Abgleich.
- Ein einzelner Test ersetzt nicht den Link auf die betroffene `RPC-*`-
  oder ADR-ID.
- Nicht vorhandene Make-Targets sind `not available`, nicht `pass`.
- SoftHSM-Evidence belegt keine Produktionsfaehigkeit.
- Generator-Aenderungen brauchen Replay-/Golden-Evidence nach
  [`harness/replay.md`](replay.md).
- Reviewer-Findings sind keine Verification-Evidence. Sie koennen
  Evidence ausloesen, aber der Verifier prueft DoD, Spec und Sensors
  separat.
