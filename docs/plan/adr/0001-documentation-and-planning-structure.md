# ADR 0001 — Dokumentations- und Planungsstruktur

**Status:** Accepted
**Datum:** 2026-05-31
**Bezug:** [Lastenheft](../../../spec/lastenheft.md), [Technische Spezifikation](../../../spec/spezifikation.md) (geplant), [Architekturüberblick](../../../spec/architecture.md) (geplant)

---

## 1. Kontext

`crypto-rpc` befindet sich in der Anforderungs- und Architekturphase.
Das Repository besitzt bereits ein normatives Dokument:

- ein vertraglich abnahmebindendes Lastenheft mit den fachlichen
  Anforderungen (`RPC-*`-Kennungen, siehe `RPC-LESE-003`),

und sieht zwei weitere normative Begleitdokumente vor (`RPC-LESE-004`,
`RPC-REF-002`):

- eine technisch verbindliche, aber ohne Lastenheftänderung
  fortschreibbare Spezifikation, die das *Wie* der Umsetzung trägt
  (`spec/spezifikation.md`),
- einen Architekturüberblick (`spec/architecture.md`).

Für die weitere Arbeit braucht das Projekt eine stabile Dokumentations-
und Planungsstruktur für:

- Architekturentscheidungen,
- Roadmap und Umsetzungsslices über alle vier gleichrangigen Domänen
  (PKCS#11 als MVP, Cloud-KMS, Netzwerk-HSM, Cloud-HSM),
- offene Folgearbeiten und Trigger-Watch-Punkte,
- später anwender- und betreibernahe Erklärungen sowie Runbooks
  (insbesondere Backend-Profile gemäß `RPC-API-CFG-002`),
- archivierte oder verworfene Ideenskizzen.

Die Struktur soll klein genug für den Projektstart bleiben, aber später
Meilensteine, weitere ADRs und Umsetzungsslices aufnehmen können. Sie
muss zudem die V-Modell-Anforderungen aus dem Lastenheft
(`RPC-LESE-001`, `RPC-LESE-002`) tragen und die Trennung zwischen
vertraglich bindendem Lastenheft und fortschreibbarer Spezifikation
(`RPC-LESE-004`) respektieren.

---

## 2. Entscheidung

### 2.1 Verzeichnisstruktur

Die Dokumentation wird wie folgt organisiert:

| Pfad                              | Zweck                                                                                                            |
| --------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| `spec/lastenheft.md`              | normatives, vertraglich abnahmebindendes Lastenheft (`RPC-*`)                                                    |
| `spec/spezifikation.md`           | technische Spezifikation: konkretes *Wie* (Protobuf-Details, Mapping-Regeln, Returncodes); technisch bindend, fortschreibbar |
| `spec/architecture.md`            | Architekturüberblick und Komponentensicht                                                                        |
| `spec/proto/`                     | Protobuf-IDL-Artefakte, sobald der Generator erste Ausgaben erzeugt                                              |
| `spec/` (weitere Dateien)         | weitere normative Vorgaben, sobald sie entstehen (z. B. Sicherheitskonzept, Betriebsmodell)                      |
| `docs/plan/adr/`                  | Architecture Decision Records                                                                                    |
| `docs/plan/adr/README.md`         | lebender ADR-Index mit Status und Schärfungs-Verweisen                                                            |
| `docs/plan/planning/open/`        | Trigger-Watch, offene Folgearbeiten und Vorabklärungen                                                            |
| `docs/plan/planning/next/`        | konkret geplante, aber noch nicht aktive Arbeit (Scope-Skizze)                                                    |
| `docs/plan/planning/in-progress/` | aktive Roadmap und laufende Slice-Pläne                                                                           |
| `docs/plan/planning/done/`        | abgeschlossene Pläne und Closure-Notizen                                                                          |
| `docs/user/`                      | anwender- und betreibernahe Dokumentation (entsteht bei Bedarf)                                                   |
| `docs/operations/`                | Betriebsprofile, Runbooks und Beispiel-Konfigurationen für Backend-Domänen (entsteht bei Bedarf)                  |
| `docs/archive/`                   | verworfene oder historische Ideenskizzen (entsteht bei Bedarf)                                                    |

`docs/user/`, `docs/operations/`, `docs/archive/` und `spec/proto/`
werden erst angelegt, wenn der erste Eintrag entsteht. ADR-Verweise auf
diese Pfade bleiben gültig, bevor das Verzeichnis existiert.

### 2.2 Dateinamen

- ADR-Dateinamen folgen dem Schema `NNNN-kurz-titel.md`
  (vierstellige Nummer, fortlaufend).
- Plan-Einträge in `open/`, `next/`, `in-progress/`, `done/` folgen dem
  Schema `NNN-kurz-titel.md` (dreistellige Nummer).
- Roadmap- und Meilenstein-Dokumente in `in-progress/` dürfen sprechende
  Namen tragen (z. B. `roadmap.md`, `M1-mvp-pkcs11.md`).

### 2.3 ADRs sind nach `Accepted` immutable

Eine ADR mit Status `Accepted` wird nicht inhaltlich überschrieben.
Spätere Korrekturen oder Schärfungen entstehen als neue ADR mit
explizitem Verweis auf die abgelöste oder geschärfte Vorgängerin. Der
ADR-Index (`docs/plan/adr/README.md`) trägt für jede geschärfte ADR die
Spalte „Schärfungen / Folge-ADRs".

### 2.4 Lebenszyklus eines Plan-Eintrags

`open/` (Trigger entsteht) → `next/` (Scope skizziert) →
`in-progress/` (Slice-Plan aktiv) → `done/` (geliefert, Closure-Notiz).

Wird ein Eintrag verworfen, wandert er nach `docs/archive/`.

### 2.5 ADR-Header-Pflichtfelder

Jede ADR trägt im Kopf mindestens:

- `Status`: `Provisional` / `Accepted` / `Superseded` / `Withdrawn`
- `Datum`: ISO-8601-Datum der jeweiligen Status-Annahme
- `Bezug`: relative Links auf Lastenheft, Spezifikation, Architektur und
  vorhergehende ADRs, sofern thematisch einschlägig

### 2.6 Trennung Lastenheft vs. Spezifikation in ADRs

ADRs treffen Entscheidungen, die entweder eine Spezifikations-Anforderung
schärfen oder eine offene Architektur-Frage schließen.

- ADRs DÜRFEN NICHT eine Lastenheft-Anforderung schärfen oder einschränken
  (das wäre eine vertragliche Änderung und benötigt einen Change Request
  am Lastenheft selbst).
- ADRs DÜRFEN technische Spezifikations-Anforderungen schärfen, sofern
  sie damit keine Lastenheft-Anforderung verletzen.
- ADRs DÜRFEN bisher ungeregelte technische Entscheidungen erstmalig
  treffen.

### 2.7 Domänen-übergreifende Pflege

`crypto-rpc` modelliert vier gleichrangige Backend-Domänen (PKCS#11,
Cloud-KMS, Netzwerk-HSM, Cloud-HSM; siehe `RPC-ZB-001`,
`RPC-FA-BACKEND-001…004`). Plan-Einträge SOLLEN im Titel oder einer
Headerzeile die betroffene(n) Domäne(n) benennen, damit Domain-
übergreifende Auswirkungen sichtbar bleiben. ADRs zu einer einzelnen
Domäne MÜSSEN explizit kennzeichnen, ob die Entscheidung für andere
Domänen analog angewendet werden soll oder bewusst beschränkt bleibt
(`RPC-NONGOAL-006`, `RPC-FA-BACKEND-004`).

---

## 3. Konsequenzen

- Das Lastenheft bleibt die Quelle fachlicher Anforderungen
  (`RPC-*`-Kennungen) und ist vertraglich abnahmebindend.
- Die Spezifikation beschreibt das technische *Wie* mit denselben
  `RPC-*`-Kennungen (im selben ID-Raum, aber je ID nur in einem
  Dokument, siehe `RPC-LESE-003`); sie ist technisch bindend und ohne
  Lastenheft-Änderung fortschreibbar.
- ADRs dokumentieren **Entscheidungen**, nicht laufende Diskussionen.
- Roadmap-Dokumente in `in-progress/` verfolgen Status, Reihenfolge und
  Abnahmeschnitte. Sie liefern später die Meilenstein-Marker (`M1`,
  `M2`, …).
- Offene Punkte werden nicht in abgeschlossenen Plänen versteckt,
  sondern unter `docs/plan/planning/open/` sichtbar gehalten.
- `docs/user/` und `docs/operations/` sind explizit getrennt von Plänen;
  Runbooks, Backend-Profil-Beispiele und Bedienanleitungen sind keine
  Architekturartefakte.
- `docs/archive/` ist explizit getrennt von `done/`: archiviert =
  verworfen oder überholt; done = umgesetzt.

---

## 4. Pflege-Regeln

- Neue fachliche Anforderungen erhalten eine `RPC-*`-Kennung im
  Lastenheft. Vertragliche Änderungen folgen einem Change-Request-
  Prozess am Lastenheft.
- Neue technische Verfahren, Datenformate oder Codes erhalten eine
  `RPC-*`-Kennung in der Spezifikation, sofern sie keine Lastenheft-
  Anforderung verletzen.
- Neue technische Entscheidungen erhalten eine ADR, wenn sie
  langfristige Auswirkungen haben oder einen `open/`-Trigger schließen.
- Jeder Plan in `in-progress/` muss Akzeptanzkriterien und einen
  Verifikationspfad enthalten.
- Abgeschlossene Pläne wandern nach `done/` mit kurzer Closure-Notiz
  (was wurde geliefert, was bleibt offen).
- Offene Trigger bleiben in `open/`, bis sie zu einem skizzierten Scope
  werden (→ `next/`), direkt aktiviert (→ `in-progress/`) oder verworfen
  (→ `archive/`) werden.
- Einträge in `next/` werden aktiviert (→ `in-progress/`), zurückgestuft
  (→ `open/`) oder verworfen (→ `archive/`).
- ADRs werden nach Erstellung nicht inhaltlich überschrieben; spätere
  Änderungen kommen als neue ADR mit Verweis auf den abgelösten
  Vorgänger.
- Bei jeder neuen ADR wird der Index in `docs/plan/adr/README.md`
  aktualisiert.

---

## 5. Nicht Gegenstand dieser ADR

- Wahl konkreter Bibliotheksversionen (z. B. `miekg/pkcs11`-Version,
  gRPC-/Protobuf-Toolchain-Version, Cloud-Provider-SDK-Versionen) —
  diese werden in eigenen ADRs entschieden, sobald sie für die
  Implementierung anstehen (`RPC-TECH-003`, `RPC-TECH-005`).
- Festlegung des Persistenz-Backends für Audit-Logs — eigene ADR
  (`RPC-FA-AUDIT-001`).
- Konkrete CI/CD-Pipeline-Pfade, Release-Workflow und Image-Registry —
  eigene ADR (`RPC-ENV-002`, `RPC-NFA-SEC-005`).
- Wahl des Sekret-Management-Backends (Kubernetes Secrets, Vault,
  Cloud-Secret-Manager) — eigene ADR oder im Betriebskonzept
  (`RPC-NFA-SEC-003`, `RPC-OPS-CFG-001`).
- Konkrete Mapping-Datei-Form und Generator-Implementierungsdetails —
  Spezifikation (`RPC-FA-GEN-002`, `RPC-NFA-MAINT-001`).
