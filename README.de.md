# crypto-rpc

**Deutsch** | [English](README.md)

`crypto-rpc` ist ein Open-Source-Monorepo für RPC-Abstraktionen über
Kryptografie-Backends. Es modelliert vier gleichrangige Domänen —
PKCS#11, Cloud-KMS, Netzwerk-HSM und Cloud-HSM — und stellt stabile,
sprachneutrale Protobuf-Kontrakte für gRPC- und TCP-RPC-
Transportprofile bereit. Der MVP bildet die PKCS#11-Schnittstelle
semantisch 1:1 ab, generiert aus den offiziellen OASIS-Headern, mit
einem Go-Referenzserver sowie generierten Go-/Java-/Kotlin-/C#-Client-
Stubs, Server-Stubs und Runtime-Source.

## Für wen?

`crypto-rpc` richtet sich an Entwickler und Plattform-Teams, die
PKCS#11-, Cloud-KMS-, Netzwerk-HSM- oder Cloud-HSM-Operationen über
Prozess-, Host- oder Sprachgrenzen hinweg nutzbar machen müssen — ohne
C-ABI, herstellerspezifische native Bibliotheken oder HSM-Treiber in
jede Client-Anwendung zu ziehen.

## Was kann ich heute ausführen?

**Noch nichts Ausführbares.** Das Repository ist Stand 2026-05-31 in
der Anforderungs- und Planungsphase. Es existieren nur die normativen
Dokumente und die Dokumentations-/Planungs-Harness:

- [`spec/lastenheft.md`](spec/lastenheft.md) — vertraglich bindendes
  Lastenheft (`RPC-*`-Kennungen)
- [`spec/spezifikation.md`](spec/spezifikation.md) — Technische
  Spezifikation (Stub)
- [`spec/architecture.md`](spec/architecture.md) — Architektur-
  Überblick (Stub)
- [`docs/plan/adr/`](docs/plan/adr/) — Architecture Decision Records
  (ADR-0001 `Accepted`)
- [`docs/plan/planning/`](docs/plan/planning/) — Open Trigger, Next,
  In-Progress (mit M1–M4-Roadmap), Done

Quellcode, Dockerfile, Makefile und CI-Pipeline kommen mit dem ersten
M1-Slice — getriggert in
[`open/001-build-container-harness.md`](docs/plan/planning/open/001-build-container-harness.md).

## Was macht es vertrauenswürdig?

- **Semantisch 1:1, nie ABI 1:1.** PKCS#11-Funktionen, Returncodes
  (`CK_RV`), Sessions, Handles, Mechanisms und Attribute behalten
  ihre Semantik; nur der Transport ändert sich (C-Pointer werden
  Protobuf-Felder). Siehe [`RPC-FA-IDL-002`](spec/lastenheft.md) und
  [`RPC-NONGOAL-001`](spec/lastenheft.md).
- **Vier gleichrangige Domänen.** PKCS#11, Cloud-KMS, Netzwerk-HSM
  und Cloud-HSM sind erstklassige Peers. Cloud-KMS liegt in einem
  getrennten `cryptorpc.kms.v1`-Paket und wird nie stillschweigend
  als PKCS#11 emuliert ([`RPC-NONGOAL-006`](spec/lastenheft.md),
  [`RPC-FA-BACKEND-004`](spec/lastenheft.md)).
- **Reproduzierbarer Generator.** IDL, Sprach-Stubs und optionale
  Runtime-Source-Artefakte werden aus gepinnten OASIS-PKCS#11-Headern
  erzeugt; identische Eingaben produzieren byte-identische Artefakte
  ([`RPC-FA-GEN-003`](spec/lastenheft.md)); Golden-File-Tests sichern
  gegen Drift.
- **Transportneutraler Vertrag.** gRPC und TCP-RPC sind
  Transportprofile über derselben fachlichen Semantik. TLS/mTLS ist
  eine Laufzeitoption pro Profil, keine Voraussetzung für IDL-,
  Mapping- oder Stub-Generierung. TCP-RPC nutzt Protobuf-Binary als
  kanonischen Payload-Codec und kann optional MessagePack unterstützen
  ([`RPC-API-TRANSPORT-*`](spec/lastenheft.md)).
- **Runtime-Source statt Black-Box-SDK.** Der Generator kann lesbaren
  Runtime-Quellcode ausgeben, optional auch in hexagonaler Adapter-
  Struktur mit Transport-, Config-, Observability-/Audit- und Backend-
  Adaptern ([`RPC-FA-GEN-007`](spec/lastenheft.md),
  [`RPC-FA-GEN-008`](spec/lastenheft.md)).
- **Returncode-Treue.** Jede fachliche Response trägt den numerischen
  `CK_RV`; Transportfehler bleiben RPC-/Netz-/Authentisierungs-Fehlern
  vorbehalten, nicht regulären PKCS#11-Fehlern
  ([`RPC-FA-P11-003`](spec/lastenheft.md),
  [`RPC-MVP-004`](spec/lastenheft.md)).
- **ADR-getriebene Entscheidungen.** Jede tragende technische
  Entscheidung wird als Architecture Decision Record unter
  [`docs/plan/adr/`](docs/plan/adr/) dokumentiert; ADR-0001
  (`Accepted` 2026-05-31) legt die Dokumentations- und Planungs-
  Struktur fest.

## Verhältnis zu den Schwesterprojekten

- [`c-hsm-doc`](https://github.com/pt9912/c-hsm-doc) — Go-Service
  für HSM-gestützte Dokumentverschlüsselung. Teilt den Go-+-CGO-+-
  PKCS#11-Stack und liefert die Build-/Container-Harness-Referenz
  (lddtree-Closure-Check, SoftHSM-Dev-Container,
  `proto-gen`/`proto-check`-Drift-Gate), die `crypto-rpc` für M1
  übernimmt.
- [`grid-gym`](https://github.com/pt9912/grid-gym) — deterministische
  Energiesystem-Simulation; gleiche docs/spec-Harness-Konventionen.
- [`grid-guide`](https://github.com/pt9912/grid-guide) — Desktop-
  Katalog-/Regel-Tool; gleiche docs/spec-Harness-Konventionen.

`crypto-rpc` ist ein unabhängiges Projekt; die Verbindung ist
methodisch (gemeinsame Dokumentations-/Planungsstruktur und
Build-Pattern), nicht funktional.

---

## Status

Stand **2026-05-31**:

- **Lastenheft v0.2 (`Entwurf, fachlich verfeinert`)** — committet;
  Artefakte auf Client-Stubs, Server-Stubs, Runtime-Source,
  Transportprofile (`grpc`, `tcp-rpc`) und optionale hexagonale
  Runtime-Source erweitert
  ([`RPC-MVP-003`](spec/lastenheft.md),
  [`RPC-NONGOAL-007`](spec/lastenheft.md),
  [`RPC-FA-GEN-007`](spec/lastenheft.md),
  [`RPC-API-TRANSPORT-*`](spec/lastenheft.md)), Release-Scope/Profilstatus-
  Vokabular ergänzt ([`RPC-LESE-007`](spec/lastenheft.md),
  [`RPC-PUE-004`](spec/lastenheft.md),
  [`RPC-FA-BACKEND-005`](spec/lastenheft.md)), neue normative Punkte
  quer durch IDL/Generator/Transport/Security/Audit/Ops/Abnahme. Code-Review von
  ADR-0001 hat 5 Findings produziert (separat zu adressieren).
- **Dokumentations-/Planungs-Harness** — bootstrappt: ADR-0001
  `Accepted`, Planning-Lifecycle-Verzeichnisse mit README-Stubs,
  M1–M4-Roadmap skizziert.
- **M1 PKCS#11-MVP** — `Pending`. Aktivierungstrigger in
  [`open/001-build-container-harness.md`](docs/plan/planning/open/001-build-container-harness.md).
- **M2 Netzwerk-HSM-Profil** — `Pending`.
- **M3 Cloud-HSM-Profil** — `Pending`.
- **M4 Cloud-KMS-API-Familie** — `Pending`.

Es gibt noch keinen Quellcode, keine Build-Pipeline und kein Runtime-
Artefakt. Heute existieren weder `make gates`, noch `Dockerfile`, noch
CI-Workflow; alle drei kommen mit M1.

### Status-Detail

| Subsystem | Stand | Belege |
| --- | --- | --- |
| Anforderungen (Lastenheft) | `Entwurf v0.2` | [`spec/lastenheft.md`](spec/lastenheft.md) — `RPC-*`-Kennungen |
| Technische Spezifikation | `Stub` | [`spec/spezifikation.md`](spec/spezifikation.md) — Kapitel-Mapping auf `RPC-*` |
| Architektur-Überblick | `Stub` | [`spec/architecture.md`](spec/architecture.md) — Abschnitt-Mapping auf `RPC-*` |
| Dokumentations-/Planungs-Harness | `Accepted` | [`ADR 0001`](docs/plan/adr/0001-documentation-and-planning-structure.md) — Verzeichnisstruktur, ADR-Lifecycle, Vier-Domänen-Pflege-Regel (§2.7) |
| Roadmap | `Entwurf` | [`docs/plan/planning/in-progress/roadmap.md`](docs/plan/planning/in-progress/roadmap.md) — M1 PKCS#11-MVP → M2 Netzwerk-HSM → M3 Cloud-HSM → M4 Cloud-KMS |
| Build-/Container-Harness | `Open` | [`open/001-build-container-harness.md`](docs/plan/planning/open/001-build-container-harness.md) — Analyse der Schwesterprojekt-Pattern, drei Optionen, Aktivierungstrigger |
| Generator + IDL | `Pending` | `RPC-FA-GEN-*`, `RPC-FA-IDL-*`; aktiviert mit M1 |
| Transportprofile | `Pending` | `RPC-API-TRANSPORT-*`; gRPC und TCP-RPC mit Protobuf-Binary plus optional MessagePack für TCP-RPC und optionalen Security-Modi `none`/`tls`/`mtls`/`external` |
| Referenzserver (Go) | `Pending` | `RPC-MVP-002`, `RPC-TECH-003`; aktiviert mit M1 |
| Sprach-Stubs + Runtime-Source (Go/Java/Kotlin/C#) | `Pending` | `RPC-MVP-003`, `RPC-NONGOAL-007`, `RPC-FA-GEN-007..009`, `RPC-API-GO/JAVA/KOTLIN/CSHARP-001`; aktiviert mit M1 |

## MVP-Scope

Der MVP (M1) MUSS laut [`spec/lastenheft.md`](spec/lastenheft.md)
Kapitel 4:

- eine Protobuf-IDL `cryptorpc.pkcs11.v1` aus gepinnten OASIS-PKCS#11-
  v3.2-Headern für 19 Kernfunktionen generieren
  ([`RPC-MVP-001`](spec/lastenheft.md))
- einen Go-Referenzserver liefern, der die Kern-API gegen SoftHSM v2
  ausführt ([`RPC-MVP-002`](spec/lastenheft.md))
- kompilierbare Go-, Java-, Kotlin- und C#-Client- **und** Server-Stubs
  emittieren ([`RPC-MVP-003`](spec/lastenheft.md)); Go bindet den
  Referenzserver gegen die generierten Server-Stubs, Java/Kotlin/C#
  brauchen nur einen Stub-Harness oder Mock-Server-Kontrakttest
  ([`RPC-NONGOAL-007`](spec/lastenheft.md))
- optional Runtime-Source ausgeben, inklusive hexagonaler Adapter-
  Scaffolds für Ports, gRPC-/TCP-RPC-Driving-Adapter, Konfiguration,
  Observability/Audit und Backend-Adapter
  ([`RPC-FA-GEN-007..009`](spec/lastenheft.md),
  [`RPC-ARCH-004`](spec/lastenheft.md))
- gRPC- und TCP-RPC-Transportprofile mit expliziten
  `tcp-rpc.codec=protobuf|messagepack`-, `security=none|tls|mtls|external`-
  und `identity.source`-Profileinstellungen unterstützen
  ([`RPC-API-TRANSPORT-*`](spec/lastenheft.md))
- PKCS#11-Returncodes (`CK_RV`) in jeder fachlichen Response erhalten
  ([`RPC-MVP-004`](spec/lastenheft.md))
- serverseitig Session- und Handle-Zustand über mehrere RPC-Calls
  verwalten ([`RPC-MVP-005`](spec/lastenheft.md))
- unterstützte/nicht unterstützte PKCS#11-Funktionen, Mechanisms und
  Attribute in einer Kompatibilitätsmatrix dokumentieren
  ([`RPC-MVP-006`](spec/lastenheft.md))
- ein Traceability-Artefakt liefern, das `UC-1`…`UC-7` auf die
  erfüllenden `RPC-*`-Anforderungen und Abnahmebelege abbildet
  ([`RPC-MVP-007`](spec/lastenheft.md))

Abnahme über `RPC-ACCEPT-001…005`.

## Geplante Funktionsbereiche

- Generator-Pipeline `crypto-rpc-gen` (Go) — parst OASIS-Header,
  wendet eine handgepflegte Mapping-Datei an, emittiert kanonische
  Protobuf-IDL, Per-Sprach-Stubs und optionale Runtime-Source-
  Artefakte
- Transportprofile für gRPC und framed TCP-RPC mit Protobuf-Binary plus
  optionalen MessagePack-Payloads und expliziten Laufzeitschaltern für
  Transport-Security und Identitätsquelle
- optionale hexagonale Runtime-Source mit Domain-Ports, Driving-
  Adaptern, Driven-Backend-Adaptern, Konfiguration, Observability und
  Audit-Integration
- Referenzserver `server/pkcs11-go` (Go) mit `miekg/pkcs11`-Binding
- Session-/Handle-Lifecycle mit Lease- und Idle-Timeout-Semantik
- Mechanism- und Attribute-Modelle mit typsicheren Varianten für
  RSA-PSS, AES-GCM, ECDH
- Backend-Profile für SoftHSM, Netzwerk-HSM, Cloud-HSM, Cloud-KMS
  (`profiles/`)
- getrennte Cloud-KMS-API-Familie `cryptorpc.kms.v1` (post-MVP) mit
  ressourcenorientierten Operationen
  (`Encrypt`/`Decrypt`/`Sign`/`Verify`/`WrapKey`/`UnwrapKey`/
  `GenerateDataKey`/`GetPublicKey`/`ListKeys`)
- Audit-Logging ohne Secret-/Klartext-/PIN-Leakage
- strukturierte Logs, Prometheus-Metriken, OCI-Containerisierung
- Supply-Chain-Posture: gepinnte Base-Images, SBOM, Dependency-Scanning

## Projektstruktur

```text
.
├── CHANGELOG.md                    ← Keep a Changelog 1.1.0 + SemVer
├── LICENSE                         ← MIT
├── README.md                       ← englische Version
├── README.de.md                    ← deutsche Version (dieses Dokument)
├── spec/
│   ├── lastenheft.md               ← normative Anforderungen (RPC-*)
│   ├── spezifikation.md            ← Technische Spezifikation (Stub)
│   └── architecture.md             ← Architektur-Überblick (Stub)
└── docs/
    └── plan/
        ├── adr/                    ← Architecture Decision Records (0001 Accepted)
        └── planning/
            ├── open/               ← Trigger-Watch (001 Build-/Container-Harness)
            ├── next/               ← geplant, Scope skizziert
            ├── in-progress/        ← aktive Roadmap (M1..M4 Skizze)
            └── done/               ← abgeschlossene Slices + Closure-Notizen
```

Code-Verzeichnisse (`cmd/`, `proto/`, `internal/`, `mappings/`,
`profiles/`, `runtime/`, `server/`, `examples/`) gemäß
[`RPC-ARCH-001`](spec/lastenheft.md) kommen mit dem ersten M1-Slice.

## Lizenz

Dieses Projekt steht unter der MIT-Lizenz. Details stehen in
[`LICENSE`](LICENSE).
