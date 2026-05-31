# Planning

Planungs- und Slice-Plan-Verzeichnis für `crypto-rpc`. Jedes
Unterverzeichnis hat einen klar abgegrenzten Lifecycle-Status:

| Verzeichnis    | Inhalt                                                                  |
| -------------- | ----------------------------------------------------------------------- |
| [`open/`](open/)               | Trigger-Watch-Notizen (Follow-up-Items, warten auf konkreten Anlass).    |
| [`next/`](next/)               | Geplante Arbeit mit Scope-Skizze, aber kein laufender Slice.             |
| [`in-progress/`](in-progress/) | Lebende Roadmap und aktive Slice-Pläne, an denen gearbeitet wird.        |
| [`done/`](done/)               | Abgeschlossene Slices und Meilensteinpläne (eingefroren, nur Referenz).  |

Ein Eintrag wechselt typischerweise:
`open/` (Trigger entsteht) → `next/` (Scope skizziert) →
`in-progress/` (Slice-Plan aktiv) → `done/` (geliefert).

Lebenszyklus und Verzeichnisstruktur sind in
[`ADR 0001`](../adr/0001-documentation-and-planning-structure.md)
§2.4 definiert.

## Domänen-Kennzeichnung

`crypto-rpc` umfasst vier gleichrangige Backend-Domänen (PKCS#11,
Cloud-KMS, Netzwerk-HSM, Cloud-HSM, siehe `RPC-ZB-001`). Plan-Einträge
SOLLEN im Dateinamen oder einer Headerzeile die betroffene(n) Domäne(n)
benennen (Beispiele: `001-pkcs11-grpc-skeleton.md`,
`005-cloud-kms-envelope-adapter.md`), damit Domain-übergreifende
Auswirkungen ohne Inhalts-Lesen sichtbar bleiben.
