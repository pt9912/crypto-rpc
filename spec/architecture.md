# Architekturüberblick — `crypto-rpc`

**Status:** Stub
**Datum:** 2026-05-31
**Bezug:** [Lastenheft](lastenheft.md), [Technische Spezifikation](spezifikation.md) (geplant)

---

Dieses Dokument trägt den Architekturüberblick und die Komponentensicht
für `crypto-rpc`. Detailentscheidungen wandern in einzelne ADRs
(`docs/plan/adr/`); konkrete Verfahren, Codes und Schemata wandern in
die [Technische Spezifikation](spezifikation.md).

Inhalte werden ergänzt, sobald die zugehörigen ADRs entstehen.

## Geplante Abschnitte

| Abschnitt                                          | Bezug Lastenheft                                            |
| -------------------------------------------------- | ----------------------------------------------------------- |
| Systemkontext und Komponentenübersicht             | `RPC-PUE-001`, `RPC-PUE-002`                                |
| Vier gleichrangige Backend-Domänen und ihre Grenzen | `RPC-ZB-001`, `RPC-FA-BACKEND-*`, `RPC-NONGOAL-006`        |
| Monorepo-Layout und Modul-Schnitt                  | `RPC-ARCH-001`, `RPC-NFA-MAINT-001`                         |
| Generator-Pipeline (`crypto-rpc-gen`)              | `RPC-FA-GEN-*`, `RPC-ARCH-002`                              |
| RPC-Verhalten (Unary, Streaming, Cancellation)     | `RPC-FA-RPC-*`                                              |
| Session-, Handle- und Lease-Modell                 | `RPC-FA-SESSION-*`, `RPC-FA-OBJ-*`                          |
| Audit- und Observability-Architektur               | `RPC-FA-AUDIT-*`, `RPC-NFA-OBS-*`                           |
| Vertrauensgrenzen und Bedrohungsmodell             | `RPC-PUE-003`, `RPC-THREAT-*`                               |
| Container-, Cluster- und Mesh-Sicht                | `RPC-ENV-*`, `RPC-NFA-HA-001`, `RPC-NFA-SCALE-001`          |
