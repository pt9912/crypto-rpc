# Harness

Dieser Harness verbindet Lastenheft, technische Spezifikation,
Architektur, ADRs, Slice-Planung und spaetere Quality-Gates fuer
`crypto-rpc`. Er ist kein Ersatz fuer `spec/` oder `docs/`, sondern der
Einstiegspunkt fuer Menschen und AI-Coding-Agenten.

Wenn diese Datei einer kanonischen Quelle widerspricht, gewinnt die
kanonische Quelle und diese Datei wird angepasst.

## Source Precedence

| Rang | Quelle | Charakter |
| --- | --- | --- |
| 1 | [`spec/lastenheft.md`](../spec/lastenheft.md) | Vertraglich abnahmebindend, `RPC-*`-IDs, Belegtypen |
| 2 | [`spec/spezifikation.md`](../spec/spezifikation.md) | Technisch verbindliches, fortschreibbares Wie |
| 3 | [`spec/architecture.md`](../spec/architecture.md) | Architekturueberblick und Komponentensicht |
| 4 | [`docs/plan/adr/`](../docs/plan/adr/) | Architekturentscheidungen und ADR-Index |
| 5 | [`docs/plan/planning/in-progress/`](../docs/plan/planning/in-progress/) und [`next/`](../docs/plan/planning/next/) | Aktive und konkret vorbereitete Slice-Arbeit |
| 6 | [`docs/plan/planning/open/`](../docs/plan/planning/open/) | Trigger-Watch, besonders Build-/Container-Harness |
| 7 | Ausfuehrbare Vertraege, sobald vorhanden | `Makefile`, `Dockerfile`, CI, Generator-Konfigurationen, Lockfiles |
| 8 | [`README.md`](../README.md), [`README.de.md`](../README.de.md), [`CHANGELOG.md`](../CHANGELOG.md) | Produktueberblick und Release-Kommunikation |
| 9 | [`AGENTS.md`](../AGENTS.md) | Agent-Briefing und Hard Rules |
| 10 | Diese Datei | Harness-Einstieg |

## Guides

Feedforward-Quellen, die Arbeit vor der Umsetzung lenken:

| Quelle | Inhalt |
| --- | --- |
| [`spec/lastenheft.md`](../spec/lastenheft.md) | `RPC-*`-Anforderungen, Belegtypen, MVP-Scope, Profilstatus |
| [`spec/spezifikation.md`](../spec/spezifikation.md) | Geplante technische Kapitel fuer Generator, Mapping, Protobuf, Runtime, Profile |
| [`spec/architecture.md`](../spec/architecture.md) | Geplante Architekturkapitel und Bezug auf Lastenheft-IDs |
| [`docs/plan/adr/README.md`](../docs/plan/adr/README.md) | ADR-Index, Folge-ADR- und Schaerfungsbeziehungen |
| [`docs/plan/adr/0001-documentation-and-planning-structure.md`](../docs/plan/adr/0001-documentation-and-planning-structure.md) | Doku-/Planning-Struktur, ADR-Disziplin, Domaenenpflege |
| [`docs/plan/planning/in-progress/roadmap.md`](../docs/plan/planning/in-progress/roadmap.md) | M1 PKCS#11-MVP bis M4 Cloud-KMS |
| [`docs/plan/planning/open/001-build-container-harness.md`](../docs/plan/planning/open/001-build-container-harness.md) | Aktivierung des spaeteren Docker-/Make-Harness |
| [`harness/roles.md`](roles.md) | Rollen, Uebergaben und Konfliktpfade |
| [`harness/review.md`](review.md) | Review-Kategorien, Prueflinsen und Output-Schema |
| [`harness/replay.md`](replay.md) | Replay-/Golden-Set-Regeln fuer Generatoren und Artefakte |
| [`harness/verification.md`](verification.md) | Verification-Evidence und Slice-Closure-Schema |
| [`AGENTS.md`](../AGENTS.md) | Hard Rules, Source Precedence, Minimal Workflow |

## Sensors

Feedback-Gates, die reale Projektzustaende messen. Stand 2026-06-02
existieren noch keine ausfuehrbaren Sensors im Repo.

| Sensor | Status | Wann verwenden |
| --- | --- | --- |
| Source-Precedence-Pruefung | manual | Bei jeder Doku-, Spec-, ADR- oder Planning-Aenderung |
| Traceability-Pruefung | manual | Jede Aenderung muss `RPC-*`, ADR oder Plan-ID kennen |
| Markdown-Link-/Pfadpruefung | manual | Solange kein `make docs-check` existiert |
| `make lint` | not available | Erst nach Build-Harness-Implementierung |
| `make test` | not available | Erst nach Code-Slice |
| `make coverage-gate` | not available | Erst nach produktivem Code und Coverage-Politik |
| `make proto-check` | not available | Erst nach Proto-/Generator-Artefakten |
| `make gates` | not available | Erst nach Makefile |
| `make ci` | not available | Erst nach CI-/Security-Sensors |
| `make fullbuild` | not available | Erst nach Runtime-Image |

Wenn ein Sensor wegen Repo-Reifegrad, Umgebung oder Sandbox nicht
laeuft, den Grund im Handoff nennen. Keine gruene Closure behaupten,
wenn der passende Sensor nicht existiert oder nicht ausgefuehrt wurde.

## Traceability

- Jede relevante Aenderung braucht einen `RPC-*`-, `ADR-*`- oder
  Plan-Anker.
- Jeder Beleg muss die erfuellten `RPC-*`-IDs nennen (`RPC-LESE-002`).
- Neue oder geaenderte Anforderungen entstehen im Lastenheft oder in der
  technischen Spezifikation nach `RPC-LESE-003` und `RPC-LESE-004`.
- ADRs duerfen das Lastenheft nicht einschraenken; technische
  Schaerfungen brauchen ADR-Index-Update.
- Neue ADRs muessen [`docs/plan/adr/README.md`](../docs/plan/adr/README.md)
  aktualisieren.
- Planning-Dokumente folgen `open/ -> next/ -> in-progress/ -> done/`.
- Abgeschlossene Slices brauchen Closure-Notiz und Verification-Evidence
  nach [`harness/verification.md`](verification.md).
- Generator-Aenderungen brauchen Replay-/Golden-Evidence nach
  [`harness/replay.md`](replay.md).

## Scope Boundaries

- `crypto-rpc` ist ein Monorepo fuer gleichrangige RPC-Domaenen:
  PKCS#11, Cloud-KMS, Netzwerk-HSM und Cloud-HSM.
- M1 ist PKCS#11-MVP gegen SoftHSM v2. SoftHSM belegt keine
  Produktionsfaehigkeit.
- PKCS#11 wird semantisch 1:1, nicht ABI 1:1 uebertragen.
- Cloud-KMS ist eine eigene API-Familie und wird nicht still als
  PKCS#11-Profil modelliert.
- `CK_RV` bleibt fachlicher Returncode; Transportfehler bleiben
  Transport-/RPC-/Auth-Fehler.
- PINs, Klartexte, Schluesselmaterial und Sensitive Attributes duerfen
  nicht in Logs, Traces, Audits, Goldens oder Fehlermeldungen geraten.
- Der Proto-Quellpfad-Konflikt `spec/proto/` vs. top-level `proto/`
  bleibt offen, bis eine Folge-ADR ihn entscheidet.
- Build-/CI-/Security-Gates werden erst mit dem M1-Harness aus
  `open/001` verbindlich.

## Minimal Agent Workflow

1. Diese Datei und [`AGENTS.md`](../AGENTS.md) lesen.
2. Rolle aus [`harness/roles.md`](roles.md) bestimmen.
3. Relevante Lastenheft-, Spezifikations-, Architektur-, ADR- und
   Slice-Doku lesen.
4. Betroffene IDs und Produktvertraege benennen.
5. Kleinste sinnvolle Aenderung ausfuehren.
6. Engste vorhandene Pruefung laufen lassen; aktuell oft manuelle
   Traceability- und Link-Pruefung.
7. Keine geplanten Make-Targets als ausgefuehrte Evidence behaupten.
8. Bei Generator-Artefakten Replay-/Golden-Evidence nach
   [`harness/replay.md`](replay.md) festhalten.
9. Bei Slice-Closure Verification-Evidence nach
   [`harness/verification.md`](verification.md) festhalten.
10. Handoff mit Rolle, geprueften Quellen, ausgefuehrten Pruefungen,
    nicht ausgefuehrten Sensors und Risiken.
