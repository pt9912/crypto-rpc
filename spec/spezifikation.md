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
| Generator-Architektur und Pipeline     | `RPC-FA-GEN-*`, `RPC-NFA-MAINT-001`          |
| Mapping-Regeln (`mappings/`-Schema)    | `RPC-FA-IDL-*`, `RPC-FA-GEN-002`             |
| Protobuf-Paketierung und Versionierung | `RPC-API-PROTO-001`                          |
| PKCS#11-Returncodes (`CK_RV`-Tabelle)  | `RPC-FA-P11-003`, `RPC-FA-ERR-001`           |
| Mechanism-Parameter-Schemata           | `RPC-FA-IDL-004`                             |
| Attribute-Modell und Sensitive-Handling | `RPC-FA-IDL-005`, `RPC-FA-OBJ-002`          |
| Session-/Handle-Lebenszyklus           | `RPC-FA-SESSION-*`, `RPC-FA-OBJ-001`         |
| Backend-Profil-Schema (`profiles/`)    | `RPC-API-CFG-002`, `RPC-FA-BACKEND-*`        |
| Cloud-KMS-IDL `cryptorpc.kms.v1`       | `RPC-API-KMS-001`, `RPC-FA-BACKEND-003`      |
| Container-Layout und Runtime-Optionen  | `RPC-ENV-002`, `RPC-TECH-001`                |
| Observability-Pipeline                 | `RPC-NFA-OBS-001`, `RPC-NFA-OBS-002`         |
