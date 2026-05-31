# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added

- `spec/lastenheft.md` v0.1 (Entwurf, `2d314c8`) — vertraglich
  abnahmebindendes Lastenheft mit dem `RPC-*`-ID-Schema
  (`RPC-LESE-003`), Belegtypen-Tabelle (`RPC-LESE-002`),
  Modalverben-Konvention nach RFC 2119 (`RPC-LESE-001`), vier
  gleichrangigen Backend-Domänen PKCS#11 / Cloud-KMS / Netzwerk-HSM /
  Cloud-HSM (`RPC-ZB-001`), 19-Funktionen-MVP-Kernumfang
  (`RPC-MVP-001`), semantisch-1:1-Abbildung statt ABI-1:1
  (`RPC-FA-IDL-002`, `RPC-NONGOAL-001`), getrennter Cloud-KMS-API-
  Familie (`RPC-FA-BACKEND-003`, `RPC-API-KMS-001`,
  `RPC-NONGOAL-006`), Returncode-Treue (`RPC-MVP-004`,
  `RPC-FA-P11-003`) und Abnahmekriterien `RPC-ACCEPT-001…007`.
  Bereits in v0.1 mit fünf Verfeinerungs-Durchgängen ausgeliefert
  (Zählerfix MVP-Funktionen 17→19 mit `C_GetMechanismList`/
  `C_GetMechanismInfo`, ZB-001/MVP-001/MENGE-001 konsistent,
  YAML als kanonisches Mapping-/Profil-Format, ID-Bereich `OPS-HC`
  entfernt, TLS-Klartextbetrieb verschärft, Glossar um `CK_RV`,
  `CK_ATTRIBUTE`, `CK_MECHANISM`, `mTLS`, `SBOM`, `IAM/RBAC`, `OCI`,
  `Lease` ergänzt, `RPC-NFA-OBS-002` Strukturierte Logs neu).
- `spec/spezifikation.md` und `spec/architecture.md` als Kapitel-/
  Abschnitts-Stubs mit Mapping-Tabellen auf `RPC-*`-Anforderungen
  (`a6054ea`).
- `docs/`-Skelett mit `plan/adr/` und
  `plan/planning/{open,next,in-progress,done}/`, jeweils mit
  `README.md`-Stub gemäss ADR-0001-Lifecycle (`a6054ea`).
- `docs/plan/adr/0001-documentation-and-planning-structure.md`
  (`Accepted` 2026-05-31, `a6054ea`) — Verzeichnisstruktur,
  `NNNN-`-ADR-/`NNN-`-Plan-Dateinamen-Konvention, Immutability-Regel
  nach `Accepted` (§2.3), Plan-Lifecycle `open/ → next/ → in-progress/
  → done/` (§2.4), ADR-Header-Pflichtfelder (`Status`, `Datum`,
  `Bezug`, §2.5), Trennung Lastenheft ↔ Spezifikation (§2.6) und
  §2.7 Domänen-übergreifende Pflege für die vier gleichrangigen
  Backend-Domänen.
- `docs/plan/adr/README.md` (ADR-Index mit Schärfungs-Spalte) und
  `docs/plan/planning/README.md` (Lifecycle-Übersicht mit Domänen-
  Kennzeichnungs-Konvention) (`a6054ea`).
- `docs/plan/planning/in-progress/roadmap.md` (`a6054ea`) — M1
  PKCS#11-MVP → M2 Netzwerk-HSM → M3 Cloud-HSM → M4 Cloud-KMS-API-
  Familie als Vorschlag, mit `RPC-ACCEPT-001…007`-Bezügen.
- `docs/plan/planning/open/001-build-container-harness.md`
  (`fa1519d`) — Trigger-Watch für Übernahme der Docker-only-Harness
  aus `c-hsm-doc`/`grid-gym`/`grid-guide`. Enthält Beobachtungs-
  Tabelle (gemeinsamer Harness-Kern), 1:1-übertragbare Bausteine aus
  `c-hsm-doc` (Stage-Kette, `lddtree`/pax-utils-Closure-Check,
  Sentinel-COPY, `dev/softhsm`-Init-Container,
  `proto-gen`/`proto-check`-Drift-Gate), drei `crypto-rpc`-spezifische
  Erweiterungen (Multi-Sprache-Stubs, vier Backend-Domänen,
  `spec/proto/` ↔ Top-Level-`proto/`-Konflikt mit `RPC-ARCH-001`),
  Optionen A/B/C und drei Aktivierungs-Bedingungen.
- `README.md` und `README.de.md` bilingual (`0e1cb92`) — Intro,
  Zielgruppe, ehrlicher Status („nichts Ausführbares — Anforderungs-
  und Planungsphase"), Trustworthiness-Anker, Verhältnis zu den
  Schwesterprojekten, Status-Tabelle, MVP-Scope-Bullets für
  `RPC-MVP-001…007` und Projektstruktur-Baum.
- `LICENSE` (MIT, `225f6a9`) — analog zu den Schwesterprojekten
  `c-hsm-doc`/`grid-gym`/`grid-guide`. Copyright `2026 crypto-rpc
  contributors`.

### Changed

- `spec/lastenheft.md` 0.1 → 0.2 (`Entwurf, fachlich verfeinert`,
  Commits `748ada1`, `cca2778`, `d594af3`):
  - Sprach-Stubs durchgängig auf **Client + Server** erweitert
    (`RPC-MVP-003`, UC-2, `RPC-API-GO/JAVA/KOTLIN/CSHARP-001`,
    `RPC-ACCEPT-002/003`, Komponententabelle `server/pkcs11-go`).
    `RPC-NONGOAL-007` neu: für Nicht-Go-Sprachen reicht im MVP ein
    Stub-Harness oder Mock-Server-Kontrakttest — keine vollständigen
    Referenzserver pro Sprache.
  - Neue Klassifikation Release-Scope / Profilstatus
    (`RPC-LESE-007`): MVP / Post-MVP / Beispielprofil / Experimentell /
    Produktionsfähig deklariert. `RPC-PUE-004` Tabelle mit MVP-
    Status pro Domäne. `RPC-FA-BACKEND-005` macht den Status pro
    Backend-Profil pflichtig.
  - Neue normative Anforderungen quer durch das Heft: `RPC-MVP-007`
    (MVP-Abnahme-Trace), `RPC-FA-IDL-006` (Größen-/Streaming-Grenzen),
    `RPC-FA-GEN-006` (Mapping-Validierung), `RPC-FA-P11-005`
    (Zwei-Phasen-Buffer-Abfragen), `RPC-FA-SESSION-004`
    (Zustandsübergänge/Aufräumen), `RPC-FA-AUDIT-003` (Audit-
    Aktivierung in produktiven Profilen), `RPC-API-CFG-003`
    (Konfigurationsvalidierung), `RPC-NFA-SEC-007` (Missbrauchs-/
    Lastbegrenzung), `RPC-NFA-OBS-003` (Trace-/Metrikhygiene),
    `RPC-NFA-OPS-002` (Runbook), `RPC-ARCH-003` (Domänentrennung im
    Code), `RPC-TECH-007` (gepinnte Generator-Toolchain),
    `RPC-COMP-005` (Artefakt-Provenienz), `RPC-THREAT-008` (Signing-
    Oracle-Missbrauch), `RPC-RISK-007` (Scope-Creep vier Domänen),
    `RPC-ENV-005` (Cloud-HSM-Betrieb), `RPC-MENGE-005` (Default-
    Limits), `RPC-ACCEPT-008` (Traceability-Abnahme), `RPC-ACCEPT-009`
    (Secure-Defaults-Abnahme).
  - Geschärft: `RPC-FA-IDL-005` Attribute-Modell um vierten Zustand
    „angefragte Länge ohne Wert"; `RPC-FA-GEN-004` Generator-Profil
    von „PKCS#11 v3.x" auf v3.2 gepinnt; `RPC-TECH-001` differenziert
    MVP-Generator/-Referenzserver (Go) vs. Server-Stub-Generierung
    für die anderen Sprachen.
- Folgedokumente an Lastenheft v0.2 ausgerichtet (`a1f5675`):
  - `README.md`/`README.de.md`: Intro und MVP-Bullet auf
    Client+Server, `RPC-NONGOAL-007`-Hinweis ergänzt, Status-Tabelle
    auf v0.2 gehoben, Änderungs-Zusammenfassung mit `RPC-LESE-007`/
    `RPC-PUE-004`/`RPC-FA-BACKEND-005`.
  - `docs/plan/planning/in-progress/roadmap.md`: M1-Stub-Bullet
    aufgeweitet (Client + Server + `RPC-NONGOAL-007`-Fallback).
  - `docs/plan/planning/open/001-build-container-harness.md`:
    Bezug-Liste um `RPC-TECH-007` und `RPC-NONGOAL-007` ergänzt,
    Multi-Sprache-Block und Option C an gelockerten MVP-Scope
    angeglichen.
  - `spec/architecture.md`: neue Abschnittszeile „Release-Scope und
    Profilstatus"; System-Kontext-, Domänen-, Monorepo- und
    Generator-Zeilen um `RPC-PUE-004`, `RPC-ARCH-003`, `RPC-TECH-007`
    erweitert.
  - `spec/spezifikation.md`: neue Kapitelzeilen für Größen-/Streaming-
    Grenzen, Server-Konfigurationsvalidierung, Runbook und Artefakt-
    Provenienz; bestehende Zeilen um `RPC-FA-GEN-006`, `RPC-FA-P11-005`,
    `RPC-FA-BACKEND-005`, `RPC-LESE-007`, `RPC-NFA-OBS-003` und
    `RPC-TECH-007` erweitert.
- `README.md`/`README.de.md` Lizenz-Sektion von „TBD/geplant" auf
  Verweis auf `LICENSE` umgestellt (`225f6a9`).

### Fixed

- Noch keine Bugfixes — Repository ist in der Anforderungs- und
  Planungsphase.
