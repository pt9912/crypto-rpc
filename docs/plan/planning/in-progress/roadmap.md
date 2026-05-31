# Roadmap — crypto-rpc

**Status:** Entwurf
**Datum:** 2026-05-31
**Bezug:** [Lastenheft](../../../../spec/lastenheft.md) (`RPC-MVP-001…006`, `RPC-ZB-003`, `RPC-FA-BACKEND-*`)

Lebende Roadmap-Skizze. Die Reihenfolge der Meilensteine ist Vorschlag,
nicht vertraglich; Lastenheft-Anforderungen sind verbindlich.

---

## M1 — PKCS#11-MVP

Liefert die MVP-Anforderungen aus `RPC-MVP-001…006` gegen SoftHSM v2
(`RPC-LESE-006`):

- Generator für Kern-API aus OASIS-Headern (`RPC-MVP-001`, `RPC-FA-GEN-*`)
- Protobuf-IDL `cryptorpc.pkcs11.v1` (`RPC-FA-IDL-*`, `RPC-API-PROTO-001`)
- Go-Referenzserver mit `miekg/pkcs11` (`RPC-MVP-002`, `RPC-TECH-003`)
- Go-/Java-/Kotlin-/C#-Stubs (`RPC-MVP-003`, `RPC-API-GO/JAVA/KOTLIN/CSHARP-001`)
- `CK_RV`-Treue (`RPC-MVP-004`, `RPC-FA-P11-003`)
- Session-/Handle-State über RPC-Calls (`RPC-MVP-005`, `RPC-FA-SESSION-*`)
- Kompatibilitätsmatrix (`RPC-MVP-006`, `docs/compatibility.md`)

Abnahme: `RPC-ACCEPT-001…005`.

## M2 — Netzwerk-HSM-Profil

Erste Vendor-PKCS#11-Anbindung gemäß `RPC-FA-BACKEND-001` /
`RPC-TECH-006` mit dokumentiertem Profil, Smoke-Test (`RPC-ACCEPT-006`)
und Operations-Notizen (`RPC-ENV-003`).

## M3 — Cloud-HSM-Profil

Cloud-HSM-Cluster-Profil (z. B. AWS CloudHSM) über die PKCS#11-API-
Familie gemäß `RPC-FA-BACKEND-002`; Smoke-Test analog `RPC-ACCEPT-006`,
plus IAM-/RBAC- und Cluster-Konfigurationsdoku.

## M4 — Cloud-KMS-API-Familie

Getrennte `cryptorpc.kms.v1`-IDL gemäß `RPC-FA-BACKEND-003`,
`RPC-API-KMS-001` mit ressourcenorientierten Operationen (`Encrypt`,
`Decrypt`, `Sign`, `Verify`, `WrapKey`, `UnwrapKey`, `GenerateDataKey`,
`GetPublicKey`, `ListKeys`). Abnahme: `RPC-ACCEPT-007`.

---

## Offene Schnittpunkte

Wird gepflegt, sobald aus `open/`-Triggern konkrete Slice-Pläne nach
`next/` wandern.
