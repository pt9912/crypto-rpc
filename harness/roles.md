# Agent Roles

Diese Datei trennt Rollen fuer AI-gestuetzte Arbeit an `crypto-rpc`.
Rollen sind Kontextgrenzen, keine Personen. Eine Person oder ein Agent
kann mehrere Rollen nacheinander ausfuehren, aber nicht mit demselben
Eingabe-Kontext und nicht ohne Uebergabe-Artefakt.

## Slice Sequence

```text
Planner -> Architect -> Implementation -> Reviewer -> Verifier -> Validator -> Planner
```

Jeder Rollenwechsel braucht ein sichtbares Artefakt: Plan, ADR-Bezug,
Diff, Findings, Verification-Evidence, Validation-Evidence oder
Closure-Notiz. Ohne Artefakt ist es kein pruefbarer Rollenwechsel.

## Role Contracts

| Rolle | Primaere Frage | Eingabe-Kontext | Output |
| --- | --- | --- | --- |
| Planner | Was wird als naechstes klein genug geliefert? | Roadmap, offene Trigger, aktive Slices, `RPC-*`-IDs | Slice-/Tranche-Plan, Lifecycle-Entscheidung, Closure-Notiz |
| Architect | Passt die Loesung zu Lastenheft, Spec, ADRs und Domaenengrenzen? | Lastenheft, Spezifikation, Architektur, ADRs, Slice-Plan | bestaetigte ADR-Bezuege, Folge-ADR-Vorschlag oder Architektur-Finding |
| Implementation | Wie wird der Slice minimal und korrekt umgesetzt? | aktiver Slice, relevante `RPC-*`-/ADR-IDs, `AGENTS.md`, engste Code-/Doku-Pfade | Code-/Doku-Diff, lokale Evidence, offene Risiken |
| Reviewer | Welche Risiken oder Vertragsbrueche enthaelt der Diff? | Plan, ADRs, Spec-Anker, Diff, relevante Tests oder fehlende Sensors | Findings mit HIGH/MEDIUM/LOW/INFO und Datei-/Zeilenbezug |
| Verifier | Erfuellt das Ergebnis DoD, `RPC-*`-IDs und ausgefuehrte Sensors? | Slice-DoD, Diff, Tests/Gates, Traceability, Handoff | Verification-Evidence, fehlende Sensors, DoD-Abweichungen |
| Validator | Loest das Ergebnis den realen Nutzer-, Betreiber- oder Release-Bedarf? | Use Case, README/Quickstart, Profile, generierte Artefakte, Runbooks | Validation-Evidence oder Rueckgabe an Planner |

## Boundaries

### Planner

Der Planner bewegt Planning-Artefakte durch
`open/ -> next/ -> in-progress/ -> done/`, schneidet zu grosse Arbeit
in Tranchen und pflegt Roadmap sowie offene Trigger. Er achtet darauf,
dass die vier Domaenen PKCS#11, Cloud-KMS, Netzwerk-HSM und Cloud-HSM
nicht versehentlich vermischt werden.

Der Planner implementiert nicht und stuft Review-Findings nicht ohne
Architektur- oder Verification-Artefakt herunter.

### Architect

Der Architect prueft Lastenheft-/Spezifikationsgrenzen, ADR-Konformitaet,
Domaenenabgrenzung, Proto-/Generator-Pfade, Security-Grenzen und
spaetere Build-/Gate-Politik.

Der Architect darf Folge-ADRs verlangen oder vorschlagen. Accepted ADRs
werden nicht stillschweigend umgeschrieben.

### Implementation

Implementation setzt nur den aktiven Scope um. Solange kein Makefile
existiert, behauptet sie keine ausfuehrbaren Gates. Sobald Code und
Build-Harness existieren, nutzt sie die engsten sinnvollen Sensors frueh
und `make gates` als normalen Code-Handoff, wenn die Umgebung es
zulaesst.

Implementation darf Konflikte zwischen Lastenheft, Spezifikation, ADR
und Slice nicht pragmatisch uebergehen. Bei Konflikt entsteht ein
Uebergabe-Artefakt an Architect oder Planner.

### Reviewer

Reviewer priorisiert Vertragsbrueche, falsche `RPC-*`-Traceability,
ADR-Drift, Domaenenverwechslung, Security-Leaks, Generator-Drift,
fehlende negative Tests und erfundene Sensors.

Findings folgen [`harness/review.md`](review.md). Reviewer verifiziert
nicht die komplette DoD-Closure. Das ist Aufgabe des Verifier.

### Verifier

Verifier prueft "built the thing right": DoD gegen Diff, `RPC-*`-IDs
gegen Evidence, Make-Targets gegen Handoff, Docs gegen Links, Generator-
Goldens gegen Replay-Regeln.

Verification-Evidence folgt [`harness/verification.md`](verification.md).
Nicht ausgefuehrte oder nicht vorhandene Sensors werden mit Grund
gelistet.

### Validator

Validator prueft "built the right thing": Passt das Ergebnis zu UC-1 bis
UC-7 im MVP, zum Betreiberpfad, zur README/Quickstart-Erwartung, zu
Profilstatus und zu den generierten Artefakten?

Fuer `crypto-rpc` sind typische Validation-Fragen:

- Versteht ein Plattformteam, welche Domaene und welcher Profilstatus
  gemeint ist?
- Erzeugen Generator und Stub-Harness spaeter erwartbare Artefakte fuer
  Go, Java, Kotlin-Zielpfad und C#?
- Ist SoftHSM-Evidence korrekt begrenzt und nicht als Produktionstest
  verkauft?
- Sind Security- und Audit-Grenzen fuer PINs, Klartexte und `CK_RV`
  sichtbar?

## Conflict Handling

| Konflikt | Entscheidungspfad |
| --- | --- |
| Reviewer meldet Lastenheft- oder ADR-Verstoss, Implementation widerspricht | Architect prueft Lastenheft, Spezifikation, ADR und Slice. Output: bestaetigtes Finding oder Folge-ADR. |
| Spezifikation oder ADR schraenkt Lastenheft ein | Lastenheft gewinnt; Architect/Planner erzeugen Korrektur- oder Folge-ADR-Pfad. |
| Verifier findet DoD-Luecke, Implementation haelt Scope fuer erledigt | Planner entscheidet: Slice zurueck, DoD anpassen oder Folge-Slice. |
| Validation ist rot, Verification ist gruen | Planner entscheidet, ob der Slice fachlich falsch geschnitten war oder ein Folge-Slice reicht. |
| Geplantes Gate wird als ausgefuehrt behauptet | Reviewer/Verifier markieren das als Finding; Handoff muss Evidence korrigieren. |
| Proto-Pfad wird ohne Folge-ADR festgelegt | Architect entscheidet Folge-ADR-Pfad; Implementation stoppt die Kanonisierung. |

## Minimal Handoff Fields

Jede Rolle uebergibt knapp:

```text
Role:
Input context:
Changed artefacts:
RPC / ADR / plan refs:
Evidence:
Open risks:
Next role:
```

Diese Felder koennen als kurzer Abschnitt in Slice-Closure, PR-Text,
Review-Kommentar oder finalem Agent-Handoff stehen.
