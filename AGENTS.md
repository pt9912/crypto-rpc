# AGENTS.md - Briefing fuer AI-Coding-Agenten

Diese Datei ist das verpflichtende Onboarding fuer jede AI-Session, die
in diesem Repo Code, Spezifikation, Planung oder Dokumentation aendert.
Sie traegt Hard Rules und Pointer auf kanonische Quellen; sie ersetzt
kein Lastenheft, keine Spezifikation, keine ADR und keinen Slice-Plan.

Bei Konflikt zwischen dieser Datei und einer kanonischen Quelle gewinnt
die kanonische Quelle. Dann ist diese Datei anzupassen.

## Source Precedence

In dieser Reihenfolge lesen und Konflikte aufloesen:

1. [`spec/lastenheft.md`](spec/lastenheft.md) - vertraglich
   abnahmebindende Anforderungen, `RPC-*`-IDs, Belegtypen und
   Scope-/Profilstatus.
2. [`spec/spezifikation.md`](spec/spezifikation.md) - technisch
   verbindliches, fortschreibbares Wie; darf das Lastenheft nicht
   einschraenken.
3. [`spec/architecture.md`](spec/architecture.md) - Architekturueberblick
   und Komponentensicht.
4. [`docs/plan/adr/`](docs/plan/adr/) - Architekturentscheidungen und
   ADR-Index. Folge-ADRs schaerfen alte Accepted ADRs, ohne sie
   umzuschreiben.
5. Aktive Planung unter
   [`docs/plan/planning/in-progress/`](docs/plan/planning/in-progress/)
   sowie konkret vorbereitete Arbeit unter
   [`docs/plan/planning/next/`](docs/plan/planning/next/).
6. Offene Trigger unter
   [`docs/plan/planning/open/`](docs/plan/planning/open/), besonders
   [`001-build-container-harness.md`](docs/plan/planning/open/001-build-container-harness.md).
7. Ausfuehrbare Projektvertraege, sobald sie existieren:
   `Makefile`, `Dockerfile`, Compose-Dateien, CI-Workflows,
   Generator-Konfigurationen, Lockfiles und Linter-Konfigurationen.
8. [`README.md`](README.md), [`README.de.md`](README.de.md) und
   [`CHANGELOG.md`](CHANGELOG.md).
9. [`harness/README.md`](harness/README.md) und diese Datei.

## Current Repository State

Stand 2026-06-02 ist `crypto-rpc` ein Spezifikations- und
Planungsrepo. Es gibt noch keinen Code, kein `Makefile`, keinen
`Dockerfile`, keine CI und keine ausfuehrbaren Gates. Keine Session darf
`make gates`, `make ci`, `go test`, Generatorlaeufe oder Containerbuilds
als Evidence behaupten, solange die entsprechenden Artefakte nicht im
Repo existieren.

Der erste Build-/Container-Harness ist als offener Trigger in
[`docs/plan/planning/open/001-build-container-harness.md`](docs/plan/planning/open/001-build-container-harness.md)
dokumentiert und wird mit dem ersten M1-Code-Slice aktiviert.

## Hard Rules

### Lastenheft gewinnt

Das Lastenheft ist vertraglich abnahmebindend (`RPC-LESE-004`). Die
Spezifikation und ADRs duerfen technische Details schaerfen, aber keine
Lastenheft-Anforderung einschraenken, umdeuten oder ersetzen. Neue
fachliche Anforderungen brauchen eine `RPC-*`-ID im Lastenheft.

### Traceability

Jede relevante Aenderung nennt die betroffenen `RPC-*`-IDs, ADRs oder
Plan-IDs. Jeder Beleg muss die erfuellten `RPC-*`-IDs nennen
(`RPC-LESE-002`). Neue oeffentliche Vertraege ohne Traceability werden
nicht als abgeschlossen uebergeben.

### Vier gleichrangige Domaenen

PKCS#11, Cloud-KMS, Netzwerk-HSM und Cloud-HSM sind gleichrangige
Ziel-Domaenen (`RPC-ZB-001`, `RPC-FA-BACKEND-*`). PKCS#11 ist der
MVP-Startpunkt, aber keine Oberklasse fuer Cloud-KMS. Domaenenspezifische
ADRs und Slice-Plaene muessen kennzeichnen, ob sie nur fuer eine Domaene
gelten oder analog fuer andere Domaenen gedacht sind (ADR-0001
Abschnitt 2.7).

### MVP-Scope nicht aufblasen

M1 liefert den PKCS#11-MVP gegen SoftHSM v2. Java/Kotlin/C# brauchen im
MVP Stub-Harness oder Mock-Server-Kontrakttests, keine vollstaendigen
Referenzserver (`RPC-NONGOAL-007`). Netzwerk-HSM, Cloud-HSM und
Cloud-KMS werden erst ab ihren jeweiligen Release-Scopes abnahmebindend.

### PKCS#11-Semantik bleibt erhalten

`crypto-rpc` bildet PKCS#11 semantisch 1:1, nie ABI 1:1 ab
(`RPC-NONGOAL-001`). `CK_RV` bleibt in fachlichen Responses erhalten;
Transportfehler sind fuer RPC-, Netzwerk- oder Authentifizierungsfehler
reserviert, nicht fuer normale PKCS#11-Returncodes.

### Generator- und Replay-Disziplin

IDL, Stubs und Runtime-Source-Artefakte muessen reproduzierbar aus
gepinnten OASIS-PKCS#11-Quellen, Mapping-Regeln und Surface Profiles
entstehen (`RPC-FA-GEN-003`). Generator-Aenderungen brauchen
Golden-/Replay-Evidence nach [`harness/replay.md`](harness/replay.md)
fuer Fresh-State, Idempotenz und relevante Safety-Pfade.

### Proto-Quellpfad nicht still entscheiden

ADR-0001 nennt `spec/proto/`, waehrend `RPC-ARCH-001` laut offenem
Trigger auf einen top-level `proto/`-Pfad zeigt. Diese Drift ist in
`open/001-build-container-harness.md` dokumentiert. Bis eine Folge-ADR
entscheidet, darf kein Code oder Generator den Pfad stillschweigend
kanonisieren.

### Docker-only ab erstem Code-Slice

Sobald Code, Generator, Stubs oder Runtime-Artefakte entstehen, gilt der
Build-/Test-Harness aus `open/001`: Host-Voraussetzungen sind Docker
und GNU `make`; Host-Toolchains duerfen nicht als alleinige Evidence
fuer einen Handoff dienen. Vorher gibt es keinen ausfuehrbaren
Docker-only-Vertrag.

### Keine erfundenen Gates

Nur reale Targets und Scripts zaehlen als Sensors. Geplante Targets wie
`make gates`, `make ci`, `proto-check`, Stub-Builds oder Security-Scans
duerfen erst als Evidence genannt werden, wenn sie im Repo implementiert
und ausgefuehrt wurden.

### Security- und Secret-Disziplin

PINs, Klartexte, Schluesselmaterial und Sensitive Attributes duerfen
nicht in Logs, Audits, Traces, Golden Files oder Fehlermeldungen landen.
SoftHSM belegt API- und Mapping-Korrektheit, aber keine
Produktionsfaehigkeit (`RPC-LESE-006`).

### ADR-Disziplin

Accepted ADRs werden nicht inhaltlich umgeschrieben (ADR-0001
Abschnitt 2.3).
Korrekturen entstehen als neue ADR oder als Folgeentscheidung mit
Index-Update in [`docs/plan/adr/README.md`](docs/plan/adr/README.md).

### Planning-Lifecycle

Planning-Artefakte folgen:

```text
open/ -> next/ -> in-progress/ -> done/
```

Lifecycle-Bewegungen erfolgen per `git mv`, damit Historie erhalten
bleibt. Abgeschlossene Slice-Plaene brauchen Closure-Notiz und
Verification-Evidence nach [`harness/verification.md`](harness/verification.md).

## Quality Gates

Aktuell existieren keine ausfuehrbaren Make-Targets. Diese Tabelle ist
der geplante Zielrahmen aus `open/001`, nicht aktuelle Evidence:

| Planned target | Status | Zweck |
| --- | --- | --- |
| `make lint` | not available | Linter, Format- und Architekturregeln |
| `make test` | not available | Unit- und Default-Tests im Container |
| `make coverage-gate` | not available | Coverage-Schwelle, sobald Code existiert |
| `make proto-check` | not available | Drift-Gate fuer generierte Proto-/Stub-Artefakte |
| `make stubs-go/java/kotlin/csharp` | not available | Sprachspezifische Stub-Kompilation oder Stub-Harness |
| `make gates` | not available | Inner-loop Aggregator |
| `make ci` | not available | Gates plus Security-/Integration-Sensors |
| `make fullbuild` | not available | Voller Buildabschluss mit Runtime-Image |

Bis diese Targets existieren, besteht Verification fuer reine
Dokumentationsaenderungen aus Source-Precedence-Pruefung,
Traceability-Pruefung und Link-/Pfadpruefung durch Review.

## Minimal Agent Workflow

1. [`harness/README.md`](harness/README.md) lesen.
2. Rolle aus [`harness/roles.md`](harness/roles.md) bestimmen.
3. Source Precedence anwenden und relevante Spec/ADR/Slice-Doku lesen.
4. Betroffene `RPC-*`-, ADR- und Plan-IDs benennen.
5. Kleinste sinnvolle Aenderung umsetzen.
6. Keine ausfuehrbaren Sensors behaupten, die nicht existieren.
7. Bei Review-Rolle [`harness/review.md`](harness/review.md) anwenden.
8. Bei Generator-/Artefakt-Aenderungen [`harness/replay.md`](harness/replay.md) anwenden.
9. Bei Slice-Closure Verification-Evidence nach
   [`harness/verification.md`](harness/verification.md) festhalten.
10. Im Handoff ausgefuehrte Pruefungen, nicht ausgefuehrte Sensors und
    verbleibende Risiken klar nennen.
