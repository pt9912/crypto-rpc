# Technische Spezifikation — `crypto-rpc`

**Status:** Stub
**Datum:** 2026-05-31
**Bezug:** [Lastenheft](lastenheft.md), [Architekturüberblick](architecture.md) (geplant)

---

Dieses Dokument trägt das technische *Wie* der `crypto-rpc`-Umsetzung
und ist als fortschreibbares Gegenstück zum vertraglich abnahme-
bindenden Lastenheft (`RPC-LESE-004`) angelegt.

Inhalte werden ergänzt, sobald die zugehörigen Lastenheft-Anforderungen
in Slice-Pläne (`docs/plan/planning/`) überführt werden.

## Geltungsregeln

- Anforderungen in dieser Spezifikation sind technisch verbindlich für
  die Implementierung, aber nicht eigenständig abnahmebindend
  (`RPC-LESE-004`).
- IDs folgen dem `RPC-*`-Schema (`RPC-LESE-003`); eine ID lebt in genau
  einem der beiden Dokumente (Lastenheft oder Spezifikation).
- Im Konfliktfall hat das Lastenheft Vorrang (`RPC-LESE-004`).

## Geplante Kapitel

| Kapitel                                | Bezug Lastenheft                             |
| -------------------------------------- | -------------------------------------------- |
| Generator-Architektur und Pipeline     | `RPC-FA-GEN-*`, `RPC-NFA-MAINT-001`, `RPC-TECH-007` |
| Mapping-Regeln (`mappings/`-Schema)    | `RPC-FA-IDL-*`, `RPC-FA-GEN-002`, `RPC-FA-GEN-006` |
| Protobuf-Paketierung und Versionierung | `RPC-API-PROTO-001`                          |
| Größen- und Streaming-Grenzen          | `RPC-FA-IDL-006`, `RPC-MENGE-005`            |
| PKCS#11-Returncodes (`CK_RV`-Tabelle)  | `RPC-FA-P11-003`, `RPC-FA-ERR-001`           |
| Mechanism-Parameter-Schemata           | `RPC-FA-IDL-004`                             |
| Attribute-Modell und Sensitive-Handling | `RPC-FA-IDL-005`, `RPC-FA-P11-005`, `RPC-FA-OBJ-002` |
| Session-/Handle-Lebenszyklus           | `RPC-FA-SESSION-*`, `RPC-FA-OBJ-001`         |
| Server-Konfigurationsvalidierung       | `RPC-API-CFG-001`, `RPC-API-CFG-003`, `RPC-ACCEPT-009` |
| Backend-Profil-Schema (`profiles/`)    | `RPC-API-CFG-002`, `RPC-FA-BACKEND-*`, `RPC-FA-BACKEND-005`, `RPC-LESE-007` |
| Cloud-KMS-IDL `cryptorpc.kms.v1`       | `RPC-API-KMS-001`, `RPC-FA-BACKEND-003`      |
| Container-Layout und Runtime-Optionen  | `RPC-ENV-002`, `RPC-TECH-001`                |
| Observability-Pipeline                 | `RPC-NFA-OBS-001`, `RPC-NFA-OBS-002`, `RPC-NFA-OBS-003` |
| Runbook für Fehlerfälle                | `RPC-NFA-OPS-002`                            |
| Artefakt-Provenienz                    | `RPC-COMP-005`, `RPC-TECH-007`               |
