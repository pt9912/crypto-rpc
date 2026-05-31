# 001 — Build- und Container-Harness aus den Schwesterprojekten übernehmen

**Status:** Open (Trigger-Watch)
**Datum:** 2026-05-31
**Bezug:** [Lastenheft](../../../../spec/lastenheft.md) — `RPC-ENV-001`, `RPC-ENV-002`, `RPC-TECH-001`, `RPC-TECH-002`, `RPC-TECH-003`, `RPC-MVP-002`, `RPC-MVP-003`, `RPC-NFA-SEC-005`, `RPC-NFA-MAINT-001`
**Schwesterprojekte:** `c-hsm-doc`, `grid-gym`, `grid-guide`
**Domäne:** PKCS#11 (M1-MVP); spätere Erweiterung auf Netzwerk-HSM/Cloud-HSM/Cloud-KMS

---

## Trigger

`crypto-rpc` hat noch keine Build-Pipeline. Sobald der erste produktive
Slice (M1 — PKCS#11-MVP, `RPC-MVP-001…007`) beginnt, brauchen wir die
gleiche reproduzierbare Docker-only-Harness wie die Schwesterprojekte:
ein Makefile mit den Aggregator-Targets `gates`/`ci`/`fullbuild`, ein
multi-stage Dockerfile mit Per-Stage-Targets, gepinnte Toolchain und
Security-Gates.

---

## Beobachtung — gemeinsamer Harness-Kern

Alle drei Schwesterprojekte (`c-hsm-doc`, `grid-gym`, `grid-guide`)
verwenden dieselbe Build-Architektur:

| Pattern | Umsetzung |
| --- | --- |
| Docker-only Build | Host braucht nur Docker + make; keine Toolchain. |
| Multi-stage Dockerfile | Jeder Gate ist eigene `FROM ... AS <stage>`; Makefile delegiert per `docker build --target`. |
| Aggregator-Targets | `gates` (inner loop) → `ci` (+ security/integration) → `fullbuild` (+ runtime image). |
| Pin-Politik | Sprach-Toolchain, Linter, Scanner per `ARG`/`?=` gepinnt; Release-Builds setzen `<tag>@sha256:...`. |
| Help-Target | Default-Goal, awk-Parser über `##`-Kommentare. |
| Security-Gate | Trivy `HIGH,CRITICAL` außerhalb des Dockerfiles + Sprach-spezifischer Dep-Audit. |
| Lock-Refresh | Eigenes `lock-refresh`-Target im pinned Container — niemals auf dem Host. |
| Coverage-Gate | Hard threshold, konfigurierbar per `--build-arg`/`?=`. |
| `--no-cache-filter` | Gezielte Stage-Invalidierung, ohne den deps-Cache zu kippen. |
| ADR-/ID-Querverweise | Stage- und Target-Header zitieren Lastenheft-IDs und ADR-Nummern. |

## Direkt übertragbar aus `c-hsm-doc` (Go + CGO + PKCS#11)

`crypto-rpc` teilt den Stack mit `c-hsm-doc`. Übertragbar 1:1:

- `deps`/`compile`/`lint`/`test`/`coverage`-Stage-Kette mit
  `CGO_ENABLED=0` als Default und `CGO_ENABLED=1` nur für den
  Adapter-Build.
- `deps-closure`-Stage (`lddtree`/pax-utils) ermittelt transitive
  .so-Closure von Vendor-PKCS#11-Modulen, kopiert nach
  `/staging/pkcs11-rootfs/`.
- `closure-check`-Stage nimmt das fertige Runtime-Rootfs als Input und
  verifiziert, dass alle Vendor-Module ohne `not found` auflösen;
  `touch /closure-check.ok` als Sentinel.
- Finale `runtime`-Stage zieht das Sentinel via
  `COPY --from=closure-check` → erzwingt den Closure-Check als
  Build-Voraussetzung im DAG (zyklenfrei).
- `pkcs11-dlopen-smoke` als Helper-Binary im Runtime-Image für
  `make smoke-dlopen` gegen ein konfigurierbares Modul
  (`SMOKE_PKCS11_MODULE`).
- `dev/softhsm/Dockerfile` + `docker-compose.dev.yml` als Init-Container
  für SoftHSM-Token in benanntem Volume (`RPC-ENV-001`).
- `proto-gen`-Stage mit gepinntem `buf`-Image plus `proto-check`-Target
  als Diff-Gate gegen eingechecktes `internal/gen/` (Drift-Erkennung
  für `RPC-FA-GEN-003` Reproduzierbarkeit).
- `govulncheck` als one-off `docker run` mit gepinnter Version.

## crypto-rpc-spezifische Erweiterungen (nicht in Schwesterprojekten)

1. **Multi-Sprache-Stubs** (`RPC-MVP-003`, `RPC-API-GO/JAVA/KOTLIN/CSHARP-001`):
   Go-, Java-, Kotlin- und C#-Stubs müssen im CI kompilieren. Optionen:
   - vier `--target stub-<lang>`-Stages mit je eigener Toolchain, oder
   - ein generischer Build-Container je Sprache, aufgerufen über
     ein eigenes `stubs-<lang>`-Target.
2. **Vier Backend-Domänen** (`RPC-FA-BACKEND-001…004`): je Domäne ein
   Smoke-Test-Profil analog `c-hsm-doc/dev/softhsm` und
   `c-hsm-doc/ci/bouncyhsm`. M1 deckt SoftHSM ab; M2/M3/M4 ergänzen
   Netzwerk-HSM/Cloud-HSM/Cloud-KMS.
3. **`spec/proto/`-Quellpfad-Frage** (siehe auch ADR-0001 §2.1 vs.
   `RPC-ARCH-001`): vor `proto-gen` muss geklärt sein, ob die
   kanonischen .proto-Dateien unter `spec/proto/` (ADR-0001 §2.1) oder
   unter top-level `proto/` (`RPC-ARCH-001`) leben. Aktuell besteht
   hier ein Widerspruch, der durch eine Folge-ADR aufgelöst werden muss
   (Bezug: Code-Review zu ADR-0001 vom 2026-05-31, Finding #1).

---

## Optionen (Scope-Vorschlag)

| Option | Scope | Aufwand |
| --- | --- | --- |
| **A** | Nur `Makefile` + `Dockerfile`-Stub auf c-hsm-doc-Niveau (deps/compile/lint/test/coverage/build); ohne PKCS#11-Closure und ohne `proto-gen` | klein |
| **B** | Wie A, plus `dev/softhsm/`-Init-Container + `docker-compose.dev.yml` für lokale SoftHSM-Anbindung; M1-PKCS#11-MVP-fertig ohne Multi-Sprache | mittel |
| **C** | Wie B, plus `proto-gen`/`proto-check`-Stages mit gepinntem buf-Image und Stub-Build-Stages für Go/Java/Kotlin/C# | groß |

Empfehlung: **B** als Einstiegs-Slice; **C** als Folge-Slice, sobald
der Generator (`RPC-FA-GEN-*`) erste IDL-Artefakte erzeugt.

---

## Aktivierungsbedingung (open/ → next/)

Eintritt nach `next/` ist erfüllt, sobald **eine** der folgenden
Bedingungen zutrifft:

1. Der erste M1-Slice startet, der Go-Code unter `cmd/`, `internal/`
   oder `server/pkcs11-go/` anlegt (dann hard-blocker — Code ohne
   Gates ist nicht reviewbar).
2. Die `spec/proto/` ↔ top-level `proto/`-Frage ist per Folge-ADR
   entschieden, sodass `proto-gen` einen verbindlichen Quellpfad hat
   (dann Option C wird machbar).
3. Ein externer Trigger (z. B. ein Schwesterprojekt) etabliert ein
   neueres Harness-Pattern, das wir hier mit aufnehmen wollen.

Bis dahin bleibt der Eintrag in `open/` und dient als Referenz für
spätere Slice-Pläne.

---

## Bezug

- `c-hsm-doc/Makefile`, `c-hsm-doc/Dockerfile`,
  `c-hsm-doc/docker-compose.dev.yml`,
  `c-hsm-doc/dev/softhsm/Dockerfile`,
  `c-hsm-doc/ci/bouncyhsm/Dockerfile`
- `grid-gym/Makefile`, `grid-gym/Dockerfile`
- `grid-guide/Makefile`, `grid-guide/Dockerfile`
- [ADR 0001](../../adr/0001-documentation-and-planning-structure.md) §2.1
  (Verzeichnisstruktur — offener Konflikt zu `RPC-ARCH-001` bzgl.
  `spec/proto/` vs. `proto/`)
- [Roadmap](../in-progress/roadmap.md) — M1 PKCS#11-MVP als
  Erstadressat dieses Triggers
