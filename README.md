# crypto-rpc

**English** | [Deutsch](README.de.md)

`crypto-rpc` is an open-source monorepo for RPC abstractions over
cryptographic backends. It models four equally-ranked domains —
PKCS#11, Cloud-KMS, Network-HSM, and Cloud-HSM — and exposes them
through stable, language-neutral Protobuf/gRPC contracts. The MVP
covers a semantically 1:1 PKCS#11 surface generated from the official
OASIS headers, with a Go reference server and generated
Go/Java/Kotlin/C# client and server stubs.

## Who is it for?

`crypto-rpc` targets developers and platform teams who need to expose
PKCS#11, Cloud-KMS, Network-HSM, or Cloud-HSM operations across
process, host, or language boundaries — without dragging C-ABI,
vendor-specific native libraries, or HSM driver footprints into every
client application.

## What can I run today?

**Nothing executable yet.** The repository is in the requirements and
planning phase as of 2026-05-31. Only the normative documents and the
documentation/planning harness exist:

- [`spec/lastenheft.md`](spec/lastenheft.md) — contractual
  requirements specification (`RPC-*` IDs, German)
- [`spec/spezifikation.md`](spec/spezifikation.md) — technical
  specification (stub)
- [`spec/architecture.md`](spec/architecture.md) — architecture
  overview (stub)
- [`docs/plan/adr/`](docs/plan/adr/) — Architecture Decision Records
  (ADR-0001 `Accepted`)
- [`docs/plan/planning/`](docs/plan/planning/) — open triggers, next,
  in-progress (with M1–M4 roadmap), done

Source code, Dockerfile, Makefile, and CI pipeline will land with the
first M1 slice — tracked as
[`open/001-build-container-harness.md`](docs/plan/planning/open/001-build-container-harness.md).

## What makes it trustworthy?

- **Semantically 1:1, never ABI 1:1.** PKCS#11 functions, return codes
  (`CK_RV`), sessions, handles, mechanisms, and attributes keep their
  semantics; only the transport changes (C pointers become Protobuf
  fields). See [`RPC-FA-IDL-002`](spec/lastenheft.md) and
  [`RPC-NONGOAL-001`](spec/lastenheft.md).
- **Four equally-ranked domains.** PKCS#11, Cloud-KMS, Network-HSM,
  and Cloud-HSM are first-class peers. Cloud-KMS lives in a separate
  `cryptorpc.kms.v1` package and is never silently emulated as PKCS#11
  ([`RPC-NONGOAL-006`](spec/lastenheft.md),
  [`RPC-FA-BACKEND-004`](spec/lastenheft.md)).
- **Reproducible generator.** IDL and language stubs are generated
  from pinned OASIS-PKCS#11 headers; identical inputs produce
  byte-identical artefacts ([`RPC-FA-GEN-003`](spec/lastenheft.md));
  golden-file tests guard against drift.
- **Return-code fidelity.** Every business response carries the
  numeric `CK_RV`; transport errors are reserved for RPC/network/
  authentication failures, not for normal PKCS#11 errors
  ([`RPC-FA-P11-003`](spec/lastenheft.md),
  [`RPC-MVP-004`](spec/lastenheft.md)).
- **ADR-driven decisions.** Every load-bearing technical decision is
  recorded as an Architecture Decision Record under
  [`docs/plan/adr/`](docs/plan/adr/); ADR-0001 (`Accepted` 2026-05-31)
  defines the documentation and planning structure.

## How does it relate to the sibling projects?

- [`c-hsm-doc`](https://github.com/pt9912/c-hsm-doc) — sibling Go
  service for HSM-backed document encryption. Shares the Go + CGO +
  PKCS#11 stack and contributes the build/container harness reference
  (lddtree closure check, SoftHSM dev container, `proto-gen`/`proto-check`
  drift gate) that `crypto-rpc` will inherit for M1.
- [`grid-gym`](https://github.com/pt9912/grid-gym) — deterministic
  energy-systems simulation; same docs/spec harness conventions.
- [`grid-guide`](https://github.com/pt9912/grid-guide) — desktop
  catalog/rule tool; same docs/spec harness conventions.

`crypto-rpc` is an independent project; the relationship is
methodological (shared documentation/planning structure and build
patterns), not functional.

---

## Status

As of **2026-05-31**:

- **Lastenheft v0.2 (`Entwurf, fachlich verfeinert`)** — committed;
  stubs widened to client + server across all four languages
  ([`RPC-MVP-003`](spec/lastenheft.md),
  [`RPC-NONGOAL-007`](spec/lastenheft.md)), release-scope/profile-
  status vocabulary added ([`RPC-LESE-007`](spec/lastenheft.md),
  [`RPC-PUE-004`](spec/lastenheft.md),
  [`RPC-FA-BACKEND-005`](spec/lastenheft.md)), ~20 new normative items
  across IDL/generator/security/audit/ops/acceptance. Code-review of
  ADR-0001 ran and surfaced 5 findings (handled separately).
- **Documentation/planning harness** — bootstrapped: ADR-0001
  `Accepted`, planning lifecycle directories with README stubs,
  M1–M4 roadmap sketched.
- **M1 PKCS#11-MVP** — `Pending`. Activation triggers tracked in
  [`open/001-build-container-harness.md`](docs/plan/planning/open/001-build-container-harness.md).
- **M2 Network-HSM profile** — `Pending`.
- **M3 Cloud-HSM profile** — `Pending`.
- **M4 Cloud-KMS API family** — `Pending`.

No source code, build pipeline, or runtime artefact exists yet. There
is no `make gates`, `Dockerfile`, or CI workflow today; all of these
land with M1.

### Status details

| Subsystem | State | References |
| --- | --- | --- |
| Requirements (Lastenheft) | `Draft v0.2` | [`spec/lastenheft.md`](spec/lastenheft.md) — `RPC-*` IDs |
| Technical specification | `Stub` | [`spec/spezifikation.md`](spec/spezifikation.md) — chapter mapping to `RPC-*` |
| Architecture overview | `Stub` | [`spec/architecture.md`](spec/architecture.md) — section mapping to `RPC-*` |
| Documentation/planning harness | `Accepted` | [`ADR 0001`](docs/plan/adr/0001-documentation-and-planning-structure.md) — directory layout, ADR lifecycle, four-domain pflege rule (§2.7) |
| Roadmap | `Draft` | [`docs/plan/planning/in-progress/roadmap.md`](docs/plan/planning/in-progress/roadmap.md) — M1 PKCS#11-MVP → M2 Network-HSM → M3 Cloud-HSM → M4 Cloud-KMS |
| Build/container harness | `Open` | [`open/001-build-container-harness.md`](docs/plan/planning/open/001-build-container-harness.md) — analysis of sibling-project patterns, three options, activation triggers |
| Generator + IDL | `Pending` | `RPC-FA-GEN-*`, `RPC-FA-IDL-*`; activates with M1 |
| Reference server (Go) | `Pending` | `RPC-MVP-002`, `RPC-TECH-003`; activates with M1 |
| Language stubs (Go/Java/Kotlin/C#, client + server) | `Pending` | `RPC-MVP-003`, `RPC-NONGOAL-007`, `RPC-API-GO/JAVA/KOTLIN/CSHARP-001`; activates with M1 |

## MVP Scope

The MVP (M1) shall, per [`spec/lastenheft.md`](spec/lastenheft.md)
chapter 4:

- generate a Protobuf IDL `cryptorpc.pkcs11.v1` from pinned OASIS
  PKCS#11 v3.2 headers covering 19 core functions
  ([`RPC-MVP-001`](spec/lastenheft.md))
- ship a Go reference server running the core API against SoftHSM v2
  ([`RPC-MVP-002`](spec/lastenheft.md))
- emit compilable Go, Java, Kotlin, and C# client and server stubs
  ([`RPC-MVP-003`](spec/lastenheft.md)); Go binds its reference server
  against the generated server stubs, Java/Kotlin/C# only need a stub
  harness or mock-server contract test
  ([`RPC-NONGOAL-007`](spec/lastenheft.md))
- preserve PKCS#11 return codes (`CK_RV`) in every business response
  ([`RPC-MVP-004`](spec/lastenheft.md))
- maintain server-side session and handle state across multiple RPC
  calls ([`RPC-MVP-005`](spec/lastenheft.md))
- document supported/unsupported PKCS#11 functions, mechanisms, and
  attributes in a compatibility matrix
  ([`RPC-MVP-006`](spec/lastenheft.md))
- provide a traceability artefact mapping `UC-1`…`UC-7` to the
  fulfilling `RPC-*` requirements and acceptance evidence
  ([`RPC-MVP-007`](spec/lastenheft.md))

Acceptance covered by `RPC-ACCEPT-001…005`.

## Planned Functional Areas

- generator pipeline `crypto-rpc-gen` (Go) — parses OASIS headers,
  applies a hand-curated mapping file, emits canonical Protobuf IDL
  and per-language stubs
- reference server `server/pkcs11-go` (Go) with `miekg/pkcs11` binding
- session/handle lifecycle with lease and idle-timeout semantics
- mechanism and attribute models with type-safe variants for RSA-PSS,
  AES-GCM, ECDH
- backend profiles for SoftHSM, Network-HSM, Cloud-HSM, Cloud-KMS
  (`profiles/`)
- separate Cloud-KMS API family `cryptorpc.kms.v1` (post-MVP) with
  resource-oriented operations (`Encrypt`/`Decrypt`/`Sign`/`Verify`/
  `WrapKey`/`UnwrapKey`/`GenerateDataKey`/`GetPublicKey`/`ListKeys`)
- audit logging without secret/plaintext/PIN leakage
- structured logs, Prometheus metrics, OCI containerisation
- supply-chain posture: pinned base images, SBOM, dependency scanning

## Project Structure

```text
.
├── README.md                       ← English version (this document)
├── README.de.md                    ← German version
├── spec/
│   ├── lastenheft.md               ← normative requirements (RPC-*)
│   ├── spezifikation.md            ← technical specification (stub)
│   └── architecture.md             ← architecture overview (stub)
└── docs/
    └── plan/
        ├── adr/                    ← Architecture Decision Records (0001 Accepted)
        └── planning/
            ├── open/               ← trigger watch (001 build/container harness)
            ├── next/               ← planned, scope sketched
            ├── in-progress/        ← active roadmap (M1..M4 sketch)
            └── done/               ← completed slices + closure notes
```

Code directories (`cmd/`, `proto/`, `internal/`, `mappings/`,
`profiles/`, `runtime/`, `server/`, `examples/`) per
[`RPC-ARCH-001`](spec/lastenheft.md) will land with the first M1 slice.

**Note:** the source documents under [`spec/`](spec/) and
[`docs/`](docs/) are written in German. This English README mirrors
the structure and key facts; for deep-dive content, consult
[`README.de.md`](README.de.md) or the German source documents.

## License

This project is licensed under the MIT License. Details are in
[`LICENSE`](LICENSE).
