# Lastenheft – `crypto-rpc` – RPC-Abbildung für PKCS#11, Cloud-KMS, Netzwerk-HSM und Cloud-HSM

| Dokument         | Lastenheft                                                                 |
| ---------------- | -------------------------------------------------------------------------- |
| Projektname      | `crypto-rpc`                                                               |
| Kurzbeschreibung | Monorepo für gleichrangige RPC-Domänen: PKCS#11, Cloud-KMS, Netzwerk-HSM und Cloud-HSM; MVP zunächst PKCS#11 |
| Zielplattform    | Linux-Container und native Linux-Server; PKCS#11-HSMs, Netzwerk-HSMs, Cloud-HSMs und Cloud-KMS-Dienste wie AWS KMS, Google Cloud KMS und Azure Key Vault / Managed HSM |
| Hauptnutzer      | Entwickler und Plattformteams, die PKCS#11-, Cloud-KMS-, Netzwerk-HSM- oder Cloud-HSM-Operationen über Prozess-, Host- oder Sprachgrenzen hinweg nutzbar machen müssen |
| Version          | 0.2                                                                        |
| Status           | Entwurf, fachlich verfeinert                                               |
| Datum            | 2026-05-31                                                                 |
| Begleitdokument  | [spec/spezifikation.md](spezifikation.md) – Technische Spezifikation; [spec/architecture.md](architecture.md) – Architekturüberblick |

---

## 0. Lesehinweise

### RPC-LESE-001 – Modalverben

In diesem Dokument haben die in Großbuchstaben geschriebenen Schlüsselwörter folgende normative Bedeutung in Anlehnung an V-Modell XT und RFC 2119:

- **MUSS** / **MÜSSEN** – verbindliche Anforderung für den zugeordneten Abnahmestand.
- **DARF NICHT** / **DÜRFEN NICHT** – ausdrücklich ausgeschlossen.
- **SOLLTE** / **SOLLEN** – geplante Eigenschaft; Abweichungen müssen begründet und dokumentiert werden.
- **KANN** / **KÖNNEN** – optionale Eigenschaft ohne Abnahmeverpflichtung.

Klein geschriebene Formen ("muss", "soll", "kann") sind beschreibend und nicht normativ.

MVP-blockierend sind ausschließlich `MUSS`-Anforderungen aus Kapitel 4 (MVP-Umfang) sowie Anforderungen, die ausdrücklich den Satzbestandteil `Der MVP MUSS` oder eine Use-Case-Kennzeichnung `(MVP)` tragen. `SOLLTE`- und `KANN`-Anforderungen blockieren die MVP-Abnahme nicht, auch wenn sie den MVP erwähnen.

Anforderungen ohne MVP-Kennzeichnung beschreiben den produktweiten Zielzustand. Sie werden abnahmebindend, sobald die betroffene Domäne oder Fähigkeit in einen Release-Scope aufgenommen wird.

### RPC-LESE-002 – Abnahme und Belegtypen

Eine Anforderung gilt nur dann als erfüllt, wenn der zugehörige Belegtyp im Repository vorliegt:

| Anforderungsklasse                  | Zulässiger Beleg                                                                   |
| ----------------------------------- | ---------------------------------------------------------------------------------- |
| Funktionale Anforderungen (`FA-*`)  | automatisierter Integrationstest gegen SoftHSM oder reproduzierbarer manueller Test |
| Generator (`FA-GEN-*`)              | Golden-File-Test für generierte IDL, Client-Stubs, Server-Stubs und Runtime-Source-Artefakte plus Parser-Test gegen gepinnte OASIS-Quellen |
| Protokoll und API (`API-*`)         | Protobuf-Artefakt, generierte Client-/Server-Stubs und Kontrakttest                 |
| Produkteinsatz und Use Cases (`PE-*`) | Trace auf funktionale Anforderungen oder automatisierter/reproduzierbarer Use-Case-Test |
| Produktübersicht und Vertrauensgrenzen (`PUE-*`) | Architektur- oder Sicherheitsbeleg gemäß Inhalt der Anforderung                         |
| Architektur (`ARCH-*`)              | Architekturentscheid (ADR) plus statischer Importcheck                             |
| Prinzipien (`PRINC-*`)              | ADR plus Lint-/Architekturtest                                                     |
| Technologie (`TECH-*`)              | ADR, Build-Konfiguration oder Abhängigkeitsbeleg                                    |
| Performance (`NFA-PERF-*`)          | reproduzierbares Benchmark-Skript plus Messprotokoll für die Referenzumgebung      |
| Sicherheit (`NFA-SEC-*`)            | automatisierter Test, Konfigurationsbeleg oder dokumentiertes Reviewprotokoll       |
| Compliance (`COMP-*`)               | Verweis auf einschlägige Norm plus Konfigurations- oder Testbeleg                   |
| Betrieb und Konfiguration (`NFA-OPS-*`, `OPS-*`, `OPS-MON-*`, `OPS-CFG-*`, `ENV-*`, `API-CFG-*`) | dokumentiertes Runbook, Containerfile, Helm-Chart, Konfigurationsschema oder Skript im Repository |
| Bedrohungsmodell (`THREAT-*`)       | dokumentierte Risiko- und Mitigationsanalyse im Sicherheitskonzept                  |
| Abnahmekriterien (`ACCEPT-*`)       | im Repository vorhandenes Abnahmeartefakt (Test, Skript, Demo-Lauf)                |
| Mengengerüst (`MENGE-*`)            | Test-, Benchmark-, Build- oder Profilbeleg gemäß genannter Dimension                |
| Übergreifende Anforderungen (`ZB-*`, `MVP-*`, `NONGOAL-*`) | Beleg gemäß thematisch passender Klasse oben (z. B. Funktional, Generator, Architektur); Akzeptanz ist in der Anforderung selbst genannt |
| Kontext- und Hilfsabschnitte (`LESE-*`, `RISK-*`, `ASSUMP-*`, `GLOSS-*`, `REF-*`) | beschreibend; kein eigener Implementierungsbeleg gefordert. Konsistenz wird über Dokumentenreview geprüft |

Jeder Beleg MUSS die erfüllten `RPC-*`-IDs nennen, damit Anforderungen, Implementierung und Abnahme rückverfolgbar bleiben.

### RPC-LESE-003 – ID-Schema

IDs folgen dem Muster `RPC-<Bereich>-<NNN>` mit dreistelliger Nummer. Bereiche im Lastenheft umfassen:

`LESE`, `ZB`, `PE`, `PUE`, `MVP`, `NONGOAL`,
`FA-IDL`, `FA-GEN`, `FA-P11`, `FA-RPC`, `FA-SESSION`, `FA-OBJ`, `FA-CRYPTO`, `FA-ERR`, `FA-AUDIT`, `FA-BACKEND`,
`API-PROTO`, `API-TRANSPORT`, `API-GO`, `API-JAVA`, `API-KOTLIN`, `API-CSHARP`, `API-CFG`, `API-KMS`,
`NFA-PERF`, `NFA-SCALE`, `NFA-HA`, `NFA-SEC`, `NFA-OBS`, `NFA-OPS`, `NFA-MAINT`, `NFA-PORT`,
`ARCH`, `PRINC`,
`TECH`, `ENV`, `OPS-MON`, `OPS-CFG`,
`COMP`, `THREAT`, `RISK`, `ASSUMP`, `ACCEPT`, `MENGE`, `GLOSS`, `REF`.

Die Technische Spezifikation (`spec/spezifikation.md`) verwendet denselben ID-Raum. Eine `RPC-...`-ID lebt in genau einem der beiden Dokumente; Cross-Referenzen funktionieren dadurch über beide Dokumente hinweg.

### RPC-LESE-004 – Verhältnis zur Technischen Spezifikation

Dieses Lastenheft beschreibt das fachliche, vertragliche und sicherheitsrelevante **WAS und WARUM**. Es bildet die Abnahme-Grundlage für `crypto-rpc`.

Die Technische Spezifikation beschreibt das **WIE**: Generator-Architektur, Mapping-Regeln, Protobuf-Details, PKCS#11-Returncodes, Mechanism-Parameter, Wire-Kompatibilität, Container-Layout und konkrete Runtime-Implementierungen.

Es gilt:

- Anforderungen in diesem Lastenheft sind abnahmebindend.
- Anforderungen in der Technischen Spezifikation sind technisch verbindlich für die Implementierung, aber nicht eigenständig abnahmebindend.
- Im Konfliktfall hat dieses Lastenheft Vorrang.

### RPC-LESE-005 – Referenzumgebung

Sofern eine Anforderung Performance, Latenz oder Durchsatz benennt, gilt – wenn nicht anders angegeben – folgende Referenzumgebung:

- Linux x86_64, Kernel ≥ 6.1
- 4 vCPU, 8 GiB RAM je Server-Replica
- gRPC oder TCP-RPC über Loopback oder lokales 10-GbE-VLAN; TLS/mTLS abhängig vom Transportprofil
- Go ≥ 1.22 für Generator und Referenzserver
- Protobuf als kanonische IDL; gRPC und TCP-RPC als Transportprofile
- HSM: SoftHSM v2 lokal als funktionale Referenz

### RPC-LESE-006 – SoftHSM-Abgrenzung

SoftHSM v2 dient ausschließlich als funktionale Referenz für Entwicklung, Unit-Tests, Generator-Sanity-Checks und Smoke-Tests im CI. SoftHSM verhält sich nicht wie ein produktives Hardware- oder Netzwerk-HSM.

Es gilt:

- Funktionale Abnahmen (RPC-ACCEPT-001) DÜRFEN gegen SoftHSM erbracht werden.
- Performance-, Failure- und Herstellerkompatibilitätsabnahmen DÜRFEN NICHT ausschließlich gegen SoftHSM erbracht werden.
- Tests gegen SoftHSM belegen API- und Mapping-Korrektheit, nicht Produktionsfähigkeit.

### RPC-LESE-007 – Release-Scope und Profilstatus

Anforderungen und Profile MÜSSEN einem nachvollziehbaren Scope- und Statusbegriff zugeordnet werden:

| Begriff | Bedeutung |
| ------- | --------- |
| MVP | Umfang, der für die erste Abnahme zwingend erfüllt sein muss. |
| Post-MVP | Zielbild oder spätere Fähigkeit; nicht MVP-blockierend, solange nicht in einen Release-Scope aufgenommen. |
| Beispielprofil | Dokumentiertes Entwicklungs-, Test- oder Demonstrationsprofil ohne Produktionszusage. |
| Experimentell | Implementiert oder skizziert, aber ohne vollständige Abnahme- und Betriebszusage. |
| Produktionsfähig deklariert | Profil oder Adapter mit dokumentierten Betriebsgrenzen, Security-Konfiguration und erfüllten domänenspezifischen Abnahmekriterien. |

Ein Backend-Profil oder Adapter DARF nur als produktionsfähig deklariert werden, wenn die zugehörigen Abnahmekriterien aus Kapitel 16 erfüllt und die Betriebsgrenzen dokumentiert sind.

---

## 1. Zielbestimmung

### RPC-ZB-001 – Projektziel

`crypto-rpc` MUSS ein Monorepo für vier gleichrangige Kryptografie-RPC-Domänen bereitstellen: PKCS#11, Cloud-KMS, Netzwerk-HSM und Cloud-HSM.

Der MVP MUSS zunächst die PKCS#11-API semantisch 1:1 abbilden und aus offiziellen OASIS-PKCS#11-Quellen reproduzierbar generierbare IDL-, Client-Stub-, Server-Stub- und Runtime-Source-Artefakte für Go, Java, Kotlin und C# erzeugen können. Cloud-KMS, Netzwerk-HSM und Cloud-HSM sind gleichrangige Ziel-Domänen, blockieren den MVP aber nicht.

Akzeptanz: Ein Referenzlauf generiert aus gepinnten OASIS-Headern und Mapping-Regeln eine Protobuf-IDL, Client- und Server-Stubs sowie Runtime-Source-Artefakte für Go/Java/Kotlin/C# und führt eine Signatur-Operation über einen Go-Referenzserver gegen SoftHSM erfolgreich aus.

### RPC-ZB-002 – Produktvision

Der Dienst SOLL Kryptografie-Backends über Prozess-, Host- und Sprachgrenzen hinweg nutzbar machen, ohne die fachliche Semantik der jeweiligen Domäne zu verstecken.

Für PKCS#11 sollen Anwendungen dieselben Operationen, Returncodes, Sessions, Handles und Mechanisms sehen, aber über stabile, sprachneutrale RPC-Typen statt über C-Pointer und C-ABI arbeiten. Für Cloud-KMS-Szenarien SOLL das Monorepo eine getrennte, ressourcenorientierte API-Familie ermöglichen, die Key-Ressourcen, Key-Versionen, `Encrypt`/`Decrypt`, `Sign`/`Verify`, `WrapKey`/`UnwrapKey` und `GenerateDataKey` modelliert. Netzwerk-HSM und Cloud-HSM SOLLEN als eigene gleichrangige Domänen mit eigenen Betriebs-, Profil- und Abnahmeanforderungen behandelt werden, auch wenn sie im PKCS#11-Fall dieselbe API-Familie nutzen können.

### RPC-ZB-003 – Muss-/Soll-/Kann-Ziele

| Klasse | Ziel                                                                                                      |
| ------ | --------------------------------------------------------------------------------------------------------- |
| MUSS   | Vier gleichrangige Domänen im Zielbild modellieren: PKCS#11, Cloud-KMS, Netzwerk-HSM und Cloud-HSM.        |
| MUSS   | MVP: semantisch 1:1 Abbildung der zentralen PKCS#11-Funktionen für Slots, Tokens, Sessions, Objects und Signatur. |
| MUSS   | MVP: Generator aus OASIS-Headern plus gepflegter Mapping-Datei für PKCS#11-RPC-Semantik.                  |
| MUSS   | MVP: Protobuf-IDL als kanonische RPC-Schnittstelle für PKCS#11.                                           |
| MUSS   | MVP: Go-Referenzserver mit internem PKCS#11-Backend über `miekg/pkcs11` oder äquivalentes Binding.        |
| MUSS   | MVP: generierte Go-, Java-, Kotlin- und C#-Client- und Server-Stubs sowie Runtime-Source-Artefakte für PKCS#11. |
| MUSS   | MVP: numerische PKCS#11-Returncodes (`CK_RV`) in jeder fachlichen Response erhalten.                      |
| SOLLTE | Mapping für Mechanism- und Attribute-Parameter typsicher modellieren, soweit die Spezifikation dies zulässt. |
| SOLLTE | Mehrere PKCS#11-Versionen über versionierte Mapping-Profile unterstützen.                                 |
| SOLLTE | Netzwerk-HSM- und Cloud-HSM-Profile als gleichrangige Domänen dokumentieren und testen.                   |
| SOLLTE | Eine getrennte Cloud-KMS-RPC-API mit AWS-/GCP-/Azure-kompatiblen Operationen bereitstellen.               |
| KANN   | Einen optionalen C-kompatiblen PKCS#11-Proxy (`libpkcs11.so`) bereitstellen.                              |

---

## 2. Produkteinsatz

### RPC-PE-001 – Anwendungsbereich

`crypto-rpc` MUSS in Umgebungen einsetzbar sein, in denen Anwendungen aus technischen oder organisatorischen Gründen nicht direkt ein lokales PKCS#11-Modul, ein Netzwerk-HSM, ein Cloud-HSM oder eine Cloud-KMS-Provider-API anbinden können, insbesondere:

- Sprachgrenzen zwischen PKCS#11-C-ABI und JVM/.NET/Go-Anwendungen,
- zentrale HSM-Zugriffsdienste in Kubernetes oder Service-Mesh-Umgebungen,
- Remote-Zugriff auf Netzwerk-HSMs mit kontrolliertem Session-, Netzwerk- und Audit-Modell,
- Cloud-HSM-Zugriff mit kontrollierten Cluster-, Client- und Betriebsprofilen,
- Cloud-KMS-Abstraktion für Provider-APIs ohne Slots, Sessions und PKCS#11-Handles,
- Test- und Simulationsumgebungen, in denen PKCS#11-Calls reproduzierbar über RPC beobachtet werden sollen.

### RPC-PE-002 – Zielgruppen und Stakeholder

| Rolle                    | Interesse                                                                            |
| ------------------------ | ------------------------------------------------------------------------------------ |
| Bibliotheksentwickler    | stabile IDL, generierte Stubs, klare Mapping-Regeln für PKCS#11-Typen                |
| Backend-Entwickler       | PKCS#11-Nutzung ohne lokale native Bibliothek oder HSM-Treiber                       |
| Plattform-/SRE-Team      | zentral betreibbarer Server, Health-Probes, Metriken, Session-Pool, klare Fehler     |
| Security-/Crypto-Officer | kontrollierter HSM-Zugriff, Auditierbarkeit, PIN-Handling, Mechanism-Policies        |
| Cloud-Plattformteam      | einheitliche KMS-/HSM-Profile für AWS, Google Cloud, Azure und Netzwerk-HSMs         |
| Auditor / Revision       | Nachvollziehbare Operationen, Returncodes und HSM-Fehler ohne Klartext-/PIN-Leakage  |

### RPC-PE-003 – Betriebsumgebung

Die primäre Betriebsumgebung MUSS sein:

- Linux x86_64 als Container oder nativer Prozess,
- mindestens eines der unterstützten Backend-Profile: PKCS#11/SoftHSM, Netzwerk-HSM, Cloud-HSM oder Cloud-KMS,
- Netzwerkinfrastruktur zwischen Client und Server für gRPC und TCP-RPC; TLS/mTLS ist profilabhängig optional.

Sekundäre Umgebungen, die im CI mitgeführt werden SOLLEN:

- lokale Entwicklerumgebung mit SoftHSM v2,
- dokumentierte Beispielprofile für Netzwerk-HSM und Cloud-HSM,
- Mock- oder Sandbox-Backends für Cloud-KMS-Profile,
- Linux ARM64 als Best-Effort,
- generierte Client- und Server-Stubs für Go, Java, Kotlin und C# ohne laufendes HSM.

### RPC-PE-004 – Anwendungsfälle

Folgende Use Cases MÜSSEN unterstützt werden:

- **UC-1 PKCS#11 Generate-IDL (MVP)**: Generator erzeugt eine versionierte Protobuf-IDL aus OASIS-Headern und Mapping-Datei.
- **UC-2 PKCS#11 Generate-Stubs (MVP)**: Generator-Workflow erzeugt Go-, Java-, Kotlin- und C#-Client- und Server-Stubs.
- **UC-3 PKCS#11 List-Slots (MVP)**: Client ruft `C_GetSlotList` und `C_GetTokenInfo` über RPC auf.
- **UC-4 PKCS#11 Open-Session/Login (MVP)**: Client eröffnet eine Session und meldet sich gemäß konfiguriertem Credential-Modell mit `C_Login` an.
- **UC-5 PKCS#11 Find-Object (MVP)**: Client führt `C_FindObjectsInit`/`C_FindObjects`/`C_FindObjectsFinal` aus.
- **UC-6 PKCS#11 Sign (MVP)**: Client führt `C_SignInit`/`C_Sign` gegen einen privaten HSM-Key aus.
- **UC-7 PKCS#11 Error-Preservation (MVP)**: Server gibt PKCS#11-Returncodes unverfälscht an den Client zurück.

Folgende Use Cases SOLLEN unterstützt werden:

- **UC-8 PKCS#11 Multi-Part-Sign**: `C_SignUpdate`/`C_SignFinal` für große Datenströme.
- **UC-9 PKCS#11 Encrypt/Decrypt**: symmetrische Verschlüsselung über `C_Encrypt*` und `C_Decrypt*`.
- **UC-10 PKCS#11 Generate-Key**: Schlüsselerzeugung über `C_GenerateKey` und `C_GenerateKeyPair`.
- **UC-11 Audit-Export**: Export fachlicher Operationen für Revisionszwecke.
- **UC-12 Netzwerk-HSM-Profil**: Betreiber konfiguriert ein Vendor-PKCS#11-Modul oder herstellerspezifisches Netzwerk-HSM-Profil mit Session-Limits, Timeouts und Health-Checks.
- **UC-13 Cloud-HSM-Profil**: Betreiber konfiguriert ein Cloud-HSM-Clusterprofil mit PKCS#11-Client, Cluster-Endpunkten, Zertifikaten, IAM/RBAC und Health-Checks.
- **UC-14 Cloud-KMS-Operation**: Client nutzt eine getrennte KMS-API für `Encrypt`, `Decrypt`, `Sign`, `Verify`, `WrapKey`, `UnwrapKey` oder `GenerateDataKey`.

---

## 3. Produktübersicht

### RPC-PUE-001 – Systemkontext

```text
+----------------------+  gRPC or TCP-RPC   +----------------------+
| Go / Java / Kotlin   |  <----------------->  | crypto-rpc-server    |
| C# Client-Stubs      |                       | Go Server-Stubs      |
+----------+-----------+                       +----------+-----------+
           ^                                              |
           | generated from proto                         | PKCS#11
           |                                              v
+----------+-----------+                       +----------------------+
| crypto-rpc-gen       |                       | PKCS#11 / Network-HSM |
| headers + spec + map |                       | Vendor PKCS#11 lib   |
+----------+-----------+                       +----------+-----------+
           |                                              |
           | post-MVP kms/v1                              | KMS SDK/API
           v                                              v
+----------------------+                       +----------------------+
| kms/v1 API family    |                       | AWS/GCP/Azure KMS    |
+----------------------+                       +----------------------+
```

### RPC-PUE-002 – Komponenten

| Komponente            | Sprache / Format | Verantwortung                                                                  |
| --------------------- | ---------------- | ------------------------------------------------------------------------------ |
| `crypto-rpc-gen`      | Go               | Parser, Modellbildung, Mapping-Anwendung, Protobuf- und Runtime-Source-Generierung |
| `proto/pkcs11/v1`     | Proto3           | kanonische RPC-IDL, Messages, Services, Returncode-Modell                      |
| `proto/kms/v1`        | Proto3           | Cloud-KMS-IDL mit ressourcenorientierten Key-Operationen                       |
| `transport/grpc`      | gRPC             | gRPC-Transportprofil, optionale TLS/mTLS-Terminierung, Mapping auf generierte Services |
| `transport/tcp-rpc`   | TCP              | Framing-basiertes TCP-RPC-Transportprofil, optionale TLS/mTLS-Terminierung, Mapping auf dieselbe fachliche RPC-Semantik |
| `server/pkcs11-go`    | Go               | PKCS#11-Referenzserver auf Basis generierter Go-Server-Stubs, Backend-Integration, Session-/Handle-Verwaltung, Audit, Metriken |
| `server/kms-go`       | Go               | KMS-Server/Adapter für Cloud-KMS-Profile (post-MVP)                            |
| `runtime/go`          | Go               | generierte Go-Client- und Go-Server-Stubs sowie als Source ausgebbare Client-/Server-Hilfen |
| `runtime/java`        | Java             | generierte Java-Client- und Java-Server-Stubs sowie als Source ausgebbare ergonomische Wrapper |
| `runtime/kotlin`      | Kotlin           | generierte oder Kotlin-kompatible Client-/Server-Stubs und als Source ausgebbare Coroutine-fähige Wrapper |
| `runtime/csharp`      | C#               | generierte .NET-Client- und .NET-Server-Stubs sowie als Source ausgebbare Wrapper |
| `mappings/`           | YAML             | manuelle Semantik-Ergänzungen für Pointer, Buffer, Structs und Sonderfälle (YAML ist kanonisch) |
| `profiles/`           | YAML             | Betriebsprofile für SoftHSM, Netzwerk-HSMs, Cloud-HSMs und Cloud-KMS-Provider (YAML ist kanonisch) |
| `third_party/oasis/`  | Header/Markdown  | gepinnte OASIS-PKCS#11-Quellen oder Reproduktionshinweise                      |

### RPC-PUE-003 – Vertrauensgrenzen

Folgende Vertrauensgrenzen MÜSSEN als solche dokumentiert und in Code/Konfiguration durchgesetzt werden:

- **Client ↔ RPC-Server**: gRPC und TCP-RPC MÜSSEN ohne TLS/mTLS generierbar und betreibbar sein. TLS 1.3 und mTLS KÖNNEN je Transportprofil aktiviert werden; Profile MÜSSEN explizit dokumentieren, ob TLS/mTLS serverseitig terminiert, extern terminiert oder nicht verwendet wird.
- **RPC-Server ↔ PKCS#11-HSM / Netzwerk-HSM / Cloud-HSM**: PKCS#11-Modulpfad, Slot-/Token-Auswahl, PIN/Secrets, Vendor-Client-Konfiguration und Netzwerk-Timeouts MÜSSEN serverseitig kontrolliert werden.
- **RPC-Server ↔ Cloud-KMS**: Cloud-Credentials, Provider-Region, Key-Ressourcen und IAM-/RBAC-Berechtigungen MÜSSEN serverseitig kontrolliert werden.
- **Generator ↔ OASIS-Quellen**: Header- und Spezifikationsversionen MÜSSEN reproduzierbar gepinnt sein.
- **Audit-Sink**: Audit-Ausgabe DARF NICHT PINs, Klartextdaten, private Schlüsselwerte oder unmaskierte Secret-Attribute enthalten.

### RPC-PUE-004 – Backend-Domänen und API-Familien

Die vier Ziel-Domänen MÜSSEN in Dokumentation, Konfiguration und Code als getrennte fachliche Domänen erkennbar bleiben:

| Domäne | Primäre API-Familie | MVP-Status | Bemerkung |
| ------ | ------------------- | ---------- | --------- |
| PKCS#11 | `pkcs11/v1` | MVP | Semantische 1:1-Abbildung der Kern-API. |
| Netzwerk-HSM | `pkcs11/v1` oder spätere eigene Extension | Post-MVP | Nutzt PKCS#11, wenn der Hersteller eine PKCS#11-Client-Library bereitstellt. |
| Cloud-HSM | `pkcs11/v1` oder spätere eigene Extension | Post-MVP | Cloud-spezifische Cluster-, Zertifikats- und IAM/RBAC-Aspekte bleiben Profilbestandteil. |
| Cloud-KMS | `kms/v1` | Post-MVP | Ressourcenorientierte API ohne PKCS#11-Sessions, Slots oder Handles. |

Eine gemeinsame Infrastruktur für Transport, Authentisierung, Observability oder Build DARF die fachliche Trennung der API-Familien nicht aufheben.

---

## 4. MVP-Umfang

### RPC-MVP-001 – Generator für Kern-API

Der MVP MUSS aus OASIS-PKCS#11-Headern und einer Mapping-Datei eine Protobuf-IDL für einen Kernumfang erzeugen.

Kernumfang: `C_Initialize`, `C_Finalize`, `C_GetInfo`, `C_GetSlotList`, `C_GetSlotInfo`, `C_GetTokenInfo`, `C_GetMechanismList`, `C_GetMechanismInfo`, `C_OpenSession`, `C_CloseSession`, `C_Login`, `C_Logout`, `C_FindObjectsInit`, `C_FindObjects`, `C_FindObjectsFinal`, `C_GetAttributeValue`, `C_SignInit`, `C_Sign`, `C_GenerateRandom` (19 Funktionen).

Akzeptanz: Golden-File-Test vergleicht die generierte IDL mit einem eingecheckten Referenzartefakt.

### RPC-MVP-002 – Go-Referenzserver gegen SoftHSM

Der MVP MUSS einen Go-Referenzserver bereitstellen, der die Kern-API gegen SoftHSM v2 ausführen kann.

Akzeptanz: Ein Integrationstest initialisiert einen SoftHSM-Token, meldet sich gemäß SoftHSM-Profil an, findet einen privaten Schlüssel und erzeugt über RPC eine gültige Signatur.

### RPC-MVP-003 – Client- und Server-Stubs

Der MVP MUSS Go-, Java-, Kotlin- und C#-Client- und Server-Stubs aus der Protobuf-IDL erzeugen. Server-Stubs umfassen mindestens die sprachtypischen Service-Interfaces, abstrakten Basisklassen oder Registrierungsartefakte, auf deren Basis eine Serverimplementierung die generierten RPC-Methoden implementieren kann.

Akzeptanz: CI-Build kompiliert alle generierten Client- und Server-Stubs ohne manuelle Nachbearbeitung. Für Go MUSS der Referenzserver gegen die generierten Go-Server-Stubs bauen. Für Java, Kotlin und C# MUSS mindestens ein Stub-Harness oder Mock-Server-Kontrakttest gegen die generierten Server-Artefakte bauen; ein vollständiges PKCS#11-Backend ist dafür nicht erforderlich.

### RPC-MVP-004 – Returncode-Treue

Der MVP MUSS PKCS#11-Returncodes als fachliche Ergebniswerte erhalten und DARF sie NICHT pauschal in Transportfehler übersetzen.

Akzeptanz: Ein Test provoziert `CKR_PIN_INCORRECT`, `CKR_OBJECT_HANDLE_INVALID` und `CKR_MECHANISM_INVALID` und prüft den numerischen `CK_RV` in der Response.

### RPC-MVP-005 – Session- und Handle-State

Der MVP MUSS serverseitig Sessions und Handles so verwalten, dass zustandsbehaftete PKCS#11-Aufrufsequenzen über mehrere RPC-Calls funktionieren.

Akzeptanz: `C_FindObjectsInit`/`C_FindObjects`/`C_FindObjectsFinal` und `C_SignInit`/`C_Sign` laufen in getrennten RPC-Calls über dieselbe Session.

### RPC-MVP-006 – Dokumentierte Grenzen

Der MVP MUSS dokumentieren, welche PKCS#11-Funktionen, Mechanisms, Attribute und Callback-/Mutex-Konzepte nicht oder noch nicht unterstützt sind.

Akzeptanz: `docs/compatibility.md` enthält eine maschinenlesbare oder tabellarische Feature-Matrix.

### RPC-MVP-007 – MVP-Abnahme-Trace

Der MVP MUSS einen Trace von `UC-1` bis `UC-7` auf die erfüllenden `RPC-*`-Anforderungen und Abnahmebelege bereitstellen.

Akzeptanz: Ein Dokument oder maschinenlesbares Artefakt im Repository ordnet jeden MVP-Use-Case mindestens einer funktionalen Anforderung und einem Abnahmebeleg zu.

---

## 5. Nicht-Ziele und Scope-Grenzen

### RPC-NONGOAL-001 – Kein ABI-identischer C-Pointer-Transport

`crypto-rpc` ist kein Versuch, C-Pointer, Prozessadressen oder C-ABI-Strukturen binär über das Netzwerk zu übertragen. Die Abbildung MUSS semantisch 1:1 sein, nicht ABI-identisch.

### RPC-NONGOAL-002 – Kein vollständiger PKCS#11-Proxy im MVP

Ein C-kompatibles `libpkcs11.so`, das bestehende Anwendungen unverändert laden können, ist nicht Teil des MVP.

### RPC-NONGOAL-003 – Kein HSM-Vendor-SDK

Das Projekt DARF vendor-spezifische APIs NICHT als implizite Ersatzsemantik für eine andere Domäne verwenden. Vendor-Erweiterungen KÖNNEN später als klar gekennzeichnete Extension-Namespaces modelliert werden.

### RPC-NONGOAL-004 – Kein Key-Management-System

`crypto-rpc` ist kein vollwertiges Key-Management-System. Schlüssel-Lifecycle-Funktionen werden nur soweit bereitgestellt, wie sie in der jeweiligen Domäne (PKCS#11, Cloud-KMS, Netzwerk-HSM, Cloud-HSM) fachlich vorgesehen sind.

### RPC-NONGOAL-005 – Keine Verheimlichung von PKCS#11-Komplexität

Das Projekt DARF PKCS#11-Semantik nicht durch eine vereinfachte Crypto-Service-API ersetzen. Wer eine abstrahierte Signatur- oder Verschlüsselungs-API benötigt, ist nicht Zielnutzer dieses Monorepos.

### RPC-NONGOAL-006 – Cloud-KMS ist keine PKCS#11-Emulation

Eine Cloud-KMS-API-Familie DARF NICHT als PKCS#11-kompatible Schnittstelle ausgegeben werden, solange sie keine Slots, Tokens, Sessions, Handles, `CK_ATTRIBUTE` und `CK_RV`-Semantik abbildet. Cloud-KMS MUSS als getrennte API-Familie dokumentiert werden.

### RPC-NONGOAL-007 – Keine vollständigen Referenzserver pro Sprache im MVP

Der MVP verlangt Client- und Server-Stubs für Go, Java, Kotlin und C#, aber keine vollständige PKCS#11-Referenzserver-Implementierung in jeder dieser Sprachen. Für Nicht-Go-Sprachen reicht im MVP ein kompilierbarer Stub-Harness oder Mock-Server-Kontrakttest, der die generierten Server-Artefakte verwendet und deren Integrationsfähigkeit nachweist.

---

## 6. Funktionale Anforderungen

### 6.1 IDL und Mapping

#### RPC-FA-IDL-001 – Protobuf als kanonische IDL

Die kanonische RPC-Schnittstelle MUSS als Protobuf v3 beschrieben werden.

Akzeptanz: `proto/pkcs11/v1/pkcs11.proto` wird im CI validiert und als Quelle für alle Client- und Server-Stubs verwendet.

#### RPC-FA-IDL-002 – Semantisch 1:1 statt ABI 1:1

Die RPC-IDL MUSS jede unterstützte PKCS#11-Funktion mit gleichem Funktionsnamen oder eindeutigem Alias abbilden und MUSS C-Pointer-/Längenpaare in transportfähige Felder übersetzen.

Beispiel: `CK_BYTE_PTR pData, CK_ULONG ulDataLen` MUSS als `bytes data` modelliert werden.

#### RPC-FA-IDL-003 – Opaque Handles

PKCS#11-Handles (`CK_SESSION_HANDLE`, `CK_OBJECT_HANDLE`) MÜSSEN im RPC als opaque numerische IDs übertragen werden. Clients DÜRFEN daraus keine lokale Bedeutung ableiten.

#### RPC-FA-IDL-004 – Mechanism-Modell

`CK_MECHANISM` MUSS als generisches Mechanism-Modell verfügbar sein. Für häufige strukturierte Parameter wie RSA-PSS, AES-GCM und ECDH SOLLTE die IDL typsichere Varianten bereitstellen.

#### RPC-FA-IDL-005 – Attribute-Modell

`CK_ATTRIBUTE` MUSS typ- und längenerhaltend abbildbar sein. Die IDL MUSS zwischen leerem Wert, angefragter Länge ohne Wert, nicht angefragtem Wert und nicht verfügbarem/sensitivem Wert unterscheiden können.

#### RPC-FA-IDL-006 – Größen- und Streaming-Grenzen

Die IDL MUSS für Byte-Felder und wiederholte Felder dokumentierte Größenlimits abbilden oder referenzieren können. Der Server MUSS diese Limits durchsetzen. Operationen, die diese Limits überschreiten können, SOLLEN eine Multi-Part- oder Streaming-Variante erhalten, statt unbegrenzte `bytes`-Felder zu erzwingen.

### 6.2 Generator

#### RPC-FA-GEN-001 – Header-Parser

Der Generator MUSS PKCS#11-Typen, Konstanten, Structs und Funktionssignaturen aus OASIS-Headern extrahieren.

#### RPC-FA-GEN-002 – Spezifikations-/Mapping-Ergänzung

Der Generator MUSS eine gepflegte Mapping-Datei verarbeiten, die Parameter-Richtung, Buffer-Längenpaare, Sonderfälle und nicht transportierbare C-Konzepte beschreibt.

#### RPC-FA-GEN-003 – Reproduzierbarkeit

Generierte Artefakte MÜSSEN reproduzierbar sein. Gleiche Eingaben MÜSSEN byte-identische IDL-, Stub- und Runtime-Source-Artefakte erzeugen.

#### RPC-FA-GEN-004 – Versionierte Profile

Der Generator MUSS mindestens ein Profil für PKCS#11 v3.2 unterstützen. Unterstützung für PKCS#11 v2.40 SOLLTE als separates Profil möglich sein.

#### RPC-FA-GEN-005 – Golden-File-Tests

Der Generator MUSS Golden-File-Tests für IDL, Konstanten und ausgewählte Service-Methoden bereitstellen.

#### RPC-FA-GEN-006 – Mapping-Validierung

Der Generator MUSS Mapping-Dateien gegen die gepinnten OASIS-Quellen validieren. Unbekannte Funktionen, nicht mehr passende Struct-Felder, widersprüchliche Parameter-Richtungen, unreferenzierte Sonderregeln und nicht deklarierte manuelle Overrides MÜSSEN den Generatorlauf abbrechen.

#### RPC-FA-GEN-007 – Runtime-Source-Ausgabe

Der Generator MUSS Runtime-Code als Source-Artefakt je unterstützter Zielsprache ausgeben können. Runtime-Source-Artefakte umfassen sprachspezifische Hilfen für Client- und Servernutzung, Transportauswahl, Fehler-/Returncode-Behandlung, Konfiguration und Test-Harnesses, soweit diese nicht rein fachliche PKCS#11-Semantik verändern.

Runtime-Source-Artefakte MÜSSEN als lesbarer Quellcode ausgegeben werden und DÜRFEN NICHT ausschließlich als Binärpaket, Container-Image oder extern aufzulösende Abhängigkeit bereitgestellt werden. Sie MÜSSEN dieselben Lizenz-, Provenienz- und Reproduzierbarkeitsregeln erfüllen wie generierte IDL- und Stub-Artefakte.

#### RPC-FA-GEN-008 – Hexagonale Runtime-Option

Das Generator-Tool SOLL Runtime-Source optional in einer hexagonalen Adapter-Struktur ausgeben können. Diese Option MUSS mindestens getrennte Ports für fachliche PKCS#11-Operationen, Transportadapter für gRPC und TCP-RPC, Konfigurationsadapter, Observability-/Audit-Adapter und Backend-Adapter vorsehen. Die hexagonale Struktur DARF NICHT Voraussetzung für die Nutzung der generierten IDL, Client-Stubs oder Server-Stubs sein.

#### RPC-FA-GEN-009 – Artefaktauswahl

Das Generator-Tool MUSS explizit auswählen lassen, welche Artefaktklassen erzeugt werden: mindestens `idl`, `client-stubs`, `server-stubs`, `runtime-source`, `hexagonal-runtime-source`, `examples` und `all`. Die Auswahl von `runtime-source` oder `hexagonal-runtime-source` DARF die fachliche IDL und das Mapping nicht verändern.

### 6.3 PKCS#11-Kernsemantik

#### RPC-FA-P11-001 – Initialisierung

`C_Initialize` und `C_Finalize` MÜSSEN serverseitig kontrolliert ausführbar sein. Mehrfachinitialisierung MUSS PKCS#11-kompatibel behandelt werden.

#### RPC-FA-P11-002 – Slot- und Token-Informationen

Slot- und Token-Funktionen MÜSSEN die relevanten PKCS#11-Felder vollständig genug zurückgeben, damit Clients Token-Label, Seriennummer, Flags und Mechanism-Verfügbarkeit auswerten können.

#### RPC-FA-P11-003 – Returncode-Erhalt

Jede fachliche PKCS#11-Response MUSS ein `CK_RV`-Feld enthalten. Transportfehler DÜRFEN nur für RPC-/Netzwerk-/Authentisierungsfehler verwendet werden, nicht für normale PKCS#11-Fehler. Ein PKCS#11-Fehler wie `CKR_PIN_INCORRECT` oder `CKR_MECHANISM_INVALID` MUSS daher als fachliche Response mit entsprechendem `CK_RV` transportiert werden.

#### RPC-FA-P11-004 – CK_ULONG-Normalisierung

`CK_ULONG` und verwandte numerische Typen MÜSSEN im RPC in einer plattformneutralen Breite abgebildet werden. Der MVP MUSS hierfür `uint64` verwenden.

#### RPC-FA-P11-005 – Zwei-Phasen-Buffer-Abfragen

PKCS#11-Aufrufe mit Längenabfrage- und Wertabfrage-Semantik, insbesondere `C_GetAttributeValue`, MÜSSEN so modelliert werden, dass Clients sowohl benötigte Längen als auch verfügbare Werte und PKCS#11-Fehlercodes unverfälscht erhalten. Sensitive oder nicht extrahierbare Werte DÜRFEN NICHT durch leere Werte maskiert werden; der Status MUSS unterscheidbar bleiben.

### 6.4 RPC-Verhalten

#### RPC-FA-RPC-001 – Unary Calls für Single-Part-Operationen

Single-Part-Operationen wie `C_Sign` und `C_GenerateRandom` MÜSSEN als Unary RPCs verfügbar sein.

#### RPC-FA-RPC-002 – Streaming für Multi-Part-Operationen

Multi-Part-Operationen wie `C_SignUpdate`/`C_SignFinal` SOLLEN als zustandsbehaftete RPC-Sequenz modelliert werden und KÖNNEN zusätzlich als gRPC-Streaming bereitgestellt werden.

#### RPC-FA-RPC-003 – Deadline und Cancellation

RPC-Deadlines und Cancellation MÜSSEN serverseitig respektiert werden. Abgebrochene Operationen MÜSSEN PKCS#11-Session-State konsistent aufräumen oder als beschädigt markieren.

### 6.5 Session- und Objektmodell

#### RPC-FA-SESSION-001 – Session-Besitz

Der Server MUSS Session-Handles einem authentisierten Client-Kontext oder einer expliziten Lease zuordnen. Ein Client DARF fremde Sessions nicht verwenden.

#### RPC-FA-SESSION-002 – Session-Leases

Sessions SOLLTEN eine konfigurierbare Idle-Timeout-Lease besitzen, damit verlorene Clients keine HSM-Sessions dauerhaft binden.

#### RPC-FA-SESSION-003 – Login-Credential-Modell

`C_Login` MUSS die PKCS#11-Login-Semantik erhalten. Produktive Profile MÜSSEN PINs und HSM-Credentials serverseitig über konfigurierte Secret-Quellen oder serverseitig auflösbare Credential-Referenzen bereitstellen. Die Übertragung roher PIN-Werte im RPC DARF nur in explizit dokumentierten Entwicklungs- oder Testprofilen erlaubt sein.

#### RPC-FA-SESSION-004 – Zustandsübergänge und Aufräumen

Der Server MUSS begonnene zustandsbehaftete Sequenzen wie Find-, Sign-, Encrypt- oder Decrypt-Operationen pro Session verfolgen. Bei Fehlern, Deadline-Abbruch, Client-Abbruch oder Lease-Ablauf MUSS der Server den PKCS#11-Zustand entweder sauber abschließen, invalidieren oder die Session schließen und diesen Zustand auditierbar machen.

#### RPC-FA-OBJ-001 – Object-Handles

Object-Handles MÜSSEN nur in der Session und dem Serverkontext gültig sein, in dem sie erzeugt oder gefunden wurden.

#### RPC-FA-OBJ-002 – Sensitive Attribute

Sensitive oder nicht lesbare Attribute DÜRFEN NICHT als Klarwert zurückgegeben werden. Der originale PKCS#11-Fehler oder ein typisiertes Unavailable-Feld MUSS erhalten bleiben.

### 6.6 Kryptografische Operationen

#### RPC-FA-CRYPTO-001 – Signatur im MVP

Der MVP MUSS `C_SignInit` und `C_Sign` für mindestens RSA-PKCS#1-v1.5 oder RSA-PSS gegen SoftHSM unterstützen.

#### RPC-FA-CRYPTO-002 – Zufall

Der MVP MUSS `C_GenerateRandom` unterstützen und den erzeugten Byte-Output als `bytes` zurückgeben.

#### RPC-FA-CRYPTO-003 – Mechanism-Fehler

Nicht unterstützte Mechanisms MÜSSEN mit dem vom PKCS#11-Backend gelieferten Returncode abgebildet werden.

#### RPC-FA-CRYPTO-004 – Private-Key-Schutz

Private Schlüsselwerte und Secret-Key-Werte DÜRFEN durch keine RPC-Funktion exportiert werden, sofern das PKCS#11-Backend sie als sensitiv oder nicht extrahierbar markiert. Für extrahierbare private oder geheime Attribute MUSS ein expliziter serverseitiger Policy-Schalter erforderlich sein; der Default MUSS die Rückgabe ablehnen. Der Server DARF sensible Attribute nicht zusätzlich loggen oder cachen.

### 6.7 Fehler und Auditierung

#### RPC-FA-ERR-001 – Fehlerklassen

Das Projekt MUSS zwischen Transportfehlern, Authentisierungs-/Autorisierungsfehlern des RPC-Servers und PKCS#11-Returncodes unterscheiden.

#### RPC-FA-AUDIT-001 – Audit-Pflichtfelder

Der Server SOLL jede kryptografische Operation mit Zeitstempel, Client-Identität, Funktion, Slot/Token, Session-ID, Mechanism, Key-Identifier soweit verfügbar, `CK_RV` und Latenz auditieren.

#### RPC-FA-AUDIT-002 – Geheimnisverbot

Audit-Logs DÜRFEN NICHT PINs, Klartextdaten, private Schlüsselwerte, Secret-Key-Werte oder rohe Signatur-Inputs enthalten.

#### RPC-FA-AUDIT-003 – Audit-Aktivierung in produktiven Profilen

Produktionsfähig deklarierte Profile MÜSSEN Audit-Ausgaben aktivieren oder an ein dokumentiertes externes Audit-System anbinden. Das Profil MUSS festlegen, welche Felder Pflichtfelder sind, welche Felder optional fehlen dürfen und wie Request-IDs mit Server-Logs und Metriken korrelieren.

### 6.8 Backend-Profile

#### RPC-FA-BACKEND-001 – Netzwerk-HSM als PKCS#11-Profil

Netzwerk-HSMs MÜSSEN als gleichrangige Backend-Domäne modelliert werden. Wenn der Zugriff über eine Vendor-PKCS#11-Client-Library erfolgt, MUSS das Profil die PKCS#11-RPC-API-Familie nutzen. Das Profil MUSS Modulpfad oder Vendor-Endpoint, Slot-/Token-Auswahl soweit zutreffend, Session-Limits, Timeouts, Health-Checks und Vendor-spezifische Umgebungsparameter dokumentieren.

#### RPC-FA-BACKEND-002 – Cloud-HSM als eigene Domäne

Cloud-HSM-Angebote MÜSSEN als gleichrangige Backend-Domäne modelliert werden. Wenn der Anbieter eine PKCS#11-Client-Library bereitstellt, z. B. AWS CloudHSM, MUSS die PKCS#11-RPC-Semantik erhalten bleiben. Cloud-spezifische Cluster-, Region-, IAM-/RBAC- und Client-Konfigurationsaspekte MÜSSEN im Cloud-HSM-Profil dokumentiert werden.

#### RPC-FA-BACKEND-003 – Cloud-KMS als getrennte API-Familie

Cloud-KMS-Dienste ohne PKCS#11-Semantik MÜSSEN außerhalb des MVP über eine getrennte `kms/v1`-API-Familie angebunden werden. Diese API SOLLTE ressourcenorientierte Operationen wie `Encrypt`, `Decrypt`, `Sign`, `Verify`, `WrapKey`, `UnwrapKey`, `GenerateDataKey`, `GetPublicKey` und `ListKeys` bereitstellen.

#### RPC-FA-BACKEND-004 – Keine implizite Semantikübersetzung

Der Server DARF Cloud-KMS-Operationen nicht stillschweigend als PKCS#11-Operationen tarnen. Jede Brücke zwischen PKCS#11 und Cloud-KMS MUSS explizit als Adapter oder Kompatibilitätsmodus dokumentiert werden.

#### RPC-FA-BACKEND-005 – Supportstatus pro Backend-Profil

Jedes Backend-Profil MUSS einen Status gemäß `RPC-LESE-007` tragen. Der Status MUSS im Profil selbst oder in einer referenzierten Profilmatrix stehen und mindestens unterstützte API-Familie, getestete Backend-Versionen, bekannte Einschränkungen, Security-Annahmen und Abnahmebelege nennen.

---

## 7. Schnittstellen

### RPC-API-PROTO-001 – Protobuf-Paket und Versionierung

Die Protobuf-IDL MUSS versionierte Pakete verwenden, z. B. `cryptorpc.pkcs11.v1` und `cryptorpc.kms.v1`. Breaking Changes MÜSSEN über neue Major-Versionen der jeweiligen IDL erfolgen.

### RPC-API-TRANSPORT-001 – Transportprofile

Der RPC-Server MUSS mindestens zwei Transportprofile unterstützen: gRPC und TCP-RPC. Beide Transportprofile MÜSSEN dieselbe fachliche PKCS#11-RPC-Semantik, dieselben `CK_RV`-Response-Regeln, dieselben Authentisierungs-/Autorisierungsentscheidungen und dieselben Auditpflichten verwenden.

### RPC-API-TRANSPORT-002 – TCP-RPC-Framing

TCP-RPC MUSS als eigenständiges, längenbegrenztes und versioniertes Framing über TCP spezifiziert werden. Das Framing MUSS Request-ID, Method-Identifier, Payload-Länge, Protokollversion und Fehlerklasse transportieren können und MUSS maximale Frame- und Message-Größen erzwingen.

### RPC-API-TRANSPORT-003 – Optionale TLS/mTLS-Terminierung

IDL, Mapping, Client-Stubs und Server-Stubs MÜSSEN unabhängig von TLS/mTLS generierbar sein. TLS 1.3 und mTLS KÖNNEN für gRPC und TCP-RPC als Laufzeitoption aktiviert werden. Wenn TLS/mTLS aktiviert ist, MUSS der Server die geprüfte Client-Identität transportneutral in einen internen Principal abbilden, damit Autorisierung, Session-Besitz und Audit nicht vom konkreten Transportprofil abhängen.

### RPC-API-TRANSPORT-004 – Tool- und Profilschalter

Generator- und Server-Tools MÜSSEN Transportprofile und Transport-Security über explizite Schalter oder Profilwerte auswählbar machen. Mindestens erforderlich sind Transportauswahl (`grpc`, `tcp-rpc`) und Security-Modus (`none`, `tls`, `mtls`, `external`). Eine Änderung des Security-Modus DARF die generierte fachliche Protobuf-IDL, Mapping-Auswertung, Client-Stubs oder Server-Stubs nicht verändern; sie DARF nur Konfiguration, Bootstrap-Code, Beispiele oder Runbooks beeinflussen.

### RPC-API-TRANSPORT-005 – Identitätsquelle und External-Termination

Wenn ein Profil Client-Identitäten verwendet, MUSS die Identitätsquelle explizit konfiguriert werden; Auto-Detection ist ausgeschlossen. Zulässige initiale Werte sind `mtls-subject`, `header` und `none`.

Für `security=mtls` MUSS `identity.source=mtls-subject` oder eine dokumentierte äquivalente Zertifikatsableitung verwendet werden. Für `security=external`, etwa bei L7-Service-Mesh- oder Reverse-Proxy-Terminierung, MUSS `identity.source=header` eine nicht leere Peer-Allowlist besitzen, bevor Header-Werte als Client-Identität akzeptiert werden. Ohne Peer-Allowlist MUSS der Serverstart fehlschlagen.

### RPC-API-TRANSPORT-006 – Betriebsmodi für Terminierung

Das Projekt MUSS mindestens folgende Transport-Security-Betriebsmodi dokumentieren:

| Modus | Beispiel | TLS/mTLS-Terminierung | Identitätsquelle |
| ----- | -------- | --------------------- | ---------------- |
| Direkt ohne TLS | lokaler Test, Entwicklungsprofil | keine | `none` oder profilabhängiger Auth-Mechanismus |
| Direkt mit TLS | Bare-Container oder Kubernetes ohne Mesh | Server | `none` oder profilabhängiger Auth-Mechanismus |
| Direkt mit mTLS | Bare-Container, Kubernetes ohne Mesh oder L4-Passthrough-Mesh | Server | `mtls-subject` |
| External Termination | L7-Service-Mesh, Sidecar oder Reverse Proxy | externer Proxy | `header` plus Peer-Allowlist |

Neue Mesh- oder Proxy-Varianten SOLLEN einem dieser Modi zugeordnet werden, solange sie keine neue Vertrauensannahme einführen.

### RPC-API-GO-001 – Go-Stubs

Das Projekt MUSS Go-Client- und Go-Server-Stubs generieren und im CI sowohl einen Go-Client-Build als auch den Go-Referenzserver gegen die generierten Service-Interfaces prüfen.

### RPC-API-JAVA-001 – Java-Stubs

Das Projekt MUSS Java-Client- und Java-Server-Stubs generieren und im CI sowohl einen Java-Client-Build als auch einen Stub-Harness oder Mock-Server-Kontrakttest gegen die generierten Service-Basisklassen prüfen.

### RPC-API-KOTLIN-001 – Kotlin-Stubs

Das Projekt MUSS Kotlin-Client- und Kotlin-Server-Stubs generieren oder Java-/Kotlin-kompatible Stubs so bereitstellen, dass Kotlin-Clients und Kotlin-Stub-Harnesses sie ohne manuelle Anpassung verwenden können.

### RPC-API-CSHARP-001 – C#-Stubs

Das Projekt MUSS C#/.NET-Client- und Server-Stubs generieren und im CI sowohl einen .NET-Client-Build als auch einen Stub-Harness oder Mock-Server-Kontrakttest gegen die generierten Service-Basisklassen prüfen.

### RPC-API-CFG-001 – Server-Konfiguration

Der Server MUSS mindestens Modulpfad, Token-/Slot-Auswahl, Secret- und Credential-Quellen, optionale Transport-Security-Konfiguration, Session-Limits und Logging/Audit-Ziel konfigurierbar machen.

### RPC-API-CFG-002 – Backend-Profile

Das Projekt MUSS eine Konfigurationsstruktur für Backend-Profile bereitstellen. Profile SOLLEN mindestens `softhsm`, `network-hsm`, `cloud-hsm` und `cloud-kms` als Klassen unterscheiden.

### RPC-API-CFG-003 – Konfigurationsvalidierung

Der Server MUSS Konfiguration beim Start validieren und bei fehlenden Pflichtfeldern, unsicheren produktiven Defaults, unbekannten Profilklassen oder widersprüchlichen Secret-Quellen mit einem eindeutigen Fehler abbrechen. Entwicklungs-Ausnahmen MÜSSEN explizit im Profil aktiviert und im Startlog sichtbar sein.

### RPC-API-KMS-001 – Cloud-KMS-IDL

Die Cloud-KMS-API-Familie MUSS außerhalb des MVP unter einem getrennten versionierten Protobuf-Paket liegen, z. B. `kms.v1` oder `cryptorpc.kms.v1`, und DARF NICHT im `pkcs11/v1`-Paket definiert werden.

---

## 8. Nichtfunktionale Anforderungen

### 8.1 Performance

#### RPC-NFA-PERF-001 – Overhead-Ziel Single-Part-Sign

Der RPC-Overhead für eine Single-Part-Signatur gegen lokales SoftHSM SOLLTE in der Referenzumgebung unter 5 ms p95 gegenüber direktem PKCS#11-Aufruf liegen.

Akzeptanz: Ein Benchmark-Skript misst direkten PKCS#11-Aufruf und RPC-Aufruf im selben Lauf, dokumentiert Stichprobengröße, Warm-up, Hardware, Backend-Profil und p95-Differenz.

#### RPC-NFA-PERF-002 – Parallelität

Der Server MUSS parallele Sessions unterstützen. Der MVP SOLL mindestens 32 gleichzeitige Sessions gegen SoftHSM ohne Session-Leak verwalten.

Akzeptanz: Ein Lasttest öffnet mindestens 32 parallele Sessions, führt pro Session eine einfache Operation aus und weist nach Testende geschlossene oder abgelaufene Sessions ohne Leaks nach.

### 8.2 Skalierbarkeit und Hochverfügbarkeit

#### RPC-NFA-SCALE-001 – Horizontale Skalierung

Der Server SOLL horizontal skalierbar sein. Session-Handles sind dabei replica-lokal, sofern keine explizite Sticky-Session- oder Lease-Strategie spezifiziert ist.

#### RPC-NFA-HA-001 – Graceful Restart

Der Server MUSS laufende Sessions bei Shutdown ablehnen, schließen oder sauber auslaufen lassen. Neue RPCs MÜSSEN während Graceful Shutdown abgewiesen werden.

### 8.3 Sicherheit

#### RPC-NFA-SEC-001 – Transportverschlüsselung

Transportverschlüsselung MUSS profilabhängig konfigurierbar sein. TLS/mTLS DARF NICHT Voraussetzung für Codegenerierung, Stub-Nutzung oder Mapping-Validierung sein. Profile ohne TLS/mTLS MÜSSEN dies explizit deklarieren; der Server MUSS beim Start sichtbar protokollieren, wenn ein Transportprofil ohne TLS betrieben wird.

#### RPC-NFA-SEC-002 – Client-Authentisierung

Client-Authentisierung MUSS profilabhängig konfigurierbar sein. mTLS KANN als Verfahren für gRPC- und TCP-RPC-Profile verwendet werden. Wenn ein Profil Client-Authentisierung aktiviert, MUSS es dokumentieren, wie Client-Identitäten authentisiert und auf serverseitige Principals abgebildet werden.

#### RPC-NFA-SEC-003 – PIN-Handling

PINs und HSM-Credentials MÜSSEN aus externen Secret-Quellen geladen werden können und DÜRFEN NICHT in Logs, Metriken, Traces oder Fehlermeldungen erscheinen. Produktive Profile MÜSSEN rohe PIN-Werte in RPC-Requests ablehnen; Entwicklungs- und Testprofile DÜRFEN sie nur nach expliziter Opt-in-Konfiguration akzeptieren.

#### RPC-NFA-SEC-004 – Autorisierung

Der Server MUSS Autorisierungsentscheidungen pro authentisiertem Client-Kontext unterstützen. Funktions-, Token- und Mechanism-Policies pro Client-Identität SOLLTEN konfigurierbar erzwingbar sein.

#### RPC-NFA-SEC-005 – Supply Chain

Builds SOLLEN SBOM, Dependency-Scanning und reproduzierbare Generator-Artefakte bereitstellen.

#### RPC-NFA-SEC-006 – Secret- und Klartextlebensdauer

Der Server MUSS PINs, HSM-Credentials, Klartextdaten und rohe Signatur-Inputs nur so lange im Speicher halten, wie sie für die jeweilige Operation erforderlich sind. Persistentes Speichern, Caching und Ausgabe in Panic-/Crash-Dumps MÜSSEN verhindert oder nachweisbar redigiert werden.

#### RPC-NFA-SEC-007 – Missbrauchs- und Lastbegrenzung

Der Server MUSS konfigurierbare Limits für gleichzeitige Sessions, offene Multi-Part-Operationen, Request-Größen, Queue-Längen und Operationsrate pro Client-Identität bereitstellen. Überschrittene Limits MÜSSEN als unterscheidbare Server-Policy- oder Ressourcenfehler auditierbar sein und DÜRFEN nicht als PKCS#11-Backendfehler getarnt werden.

#### RPC-NFA-SEC-008 – Header-Identität nur mit Peer-Allowlist

Profile mit `identity.source=header` MÜSSEN eine nicht leere Allowlist vertrauenswürdiger Peers oder Proxy-Identitäten konfigurieren. Der Server MUSS Anfragen von nicht erlaubten Peers ablehnen, bevor Header-Identitäten ausgewertet werden. Header-Identität DARF NICHT als Fallback für fehlende mTLS-Identität verwendet werden.

### 8.4 Observability und Betrieb

#### RPC-NFA-OBS-001 – Metriken

Der Server MUSS Metriken für RPC-Latenz, PKCS#11-Latenz, Returncodes, Session-Pool-Auslastung und HSM-Fehler bereitstellen.

#### RPC-NFA-OBS-002 – Strukturierte Logs

Der Server MUSS strukturierte Logs (z. B. JSON-Lines) mit korrelierbarer Request-ID, Client-Identität, Funktion, Slot/Token, Session-ID, `CK_RV` und Latenz ausgeben. Logs MÜSSEN die Geheimnisverbote aus `RPC-FA-AUDIT-002` und `RPC-NFA-SEC-003` einhalten.

#### RPC-NFA-OBS-003 – Trace- und Metrikhygiene

Traces, Metriken und Labels DÜRFEN keine hochkardinalen Secret-, Klartext-, Signaturinput-, Schlüsselwert- oder vollständigen Objektattributdaten enthalten. Key- und Object-Identifier MÜSSEN pseudonymisiert, gekürzt oder über ein dokumentiertes Allowlist-Schema begrenzt werden.

#### RPC-NFA-OPS-001 – Health und Readiness

Der Server MUSS Liveness und Readiness trennen. Readiness MUSS HSM-Erreichbarkeit und Session-Eröffnungsfähigkeit berücksichtigen.

#### RPC-NFA-OPS-002 – Runbook für Fehlerfälle

Produktionsfähig deklarierte Profile MÜSSEN ein Runbook für HSM-Verbindungsfehler, Session-Erschöpfung, Credential-Rotation, TLS-Zertifikatswechsel, Backend-Failover und Graceful Shutdown enthalten.

### 8.5 Wartbarkeit und Portabilität

#### RPC-NFA-MAINT-001 – Generator-Trennung

Parser, internes Modell, Mapping-Regeln und Ausgabegeneratoren MÜSSEN als getrennte Module implementiert werden.

#### RPC-NFA-PORT-001 – Plattform

Generator und Go-Referenzserver MÜSSEN auf Linux x86_64 laufen. Linux ARM64 SOLLTE unterstützt werden.

---

## 9. Architekturvorgaben

### RPC-ARCH-001 – Monorepo-Struktur

Das Repository SOLL folgende Top-Level-Struktur verwenden:

```text
crypto-rpc/
  cmd/
  proto/
    pkcs11/
    kms/
  internal/
  mappings/
  profiles/
  runtime/
  server/
    pkcs11-go/
    kms-go/
  examples/
  docs/
  spec/
```

### RPC-ARCH-002 – Kanonisches Zwischenmodell

Der Generator MUSS ein kanonisches internes Modell aus Headern und Mapping-Regeln bilden. Ausgaben für Protobuf und Dokumentation DÜRFEN NICHT direkt aus Header-Parser-Events erzeugt werden.

### RPC-ARCH-003 – Domänentrennung im Code

PKCS#11-, Cloud-KMS-, Netzwerk-HSM- und Cloud-HSM-spezifische Pakete MÜSSEN so getrennt sein, dass eine Domäne keine fachliche Semantik einer anderen Domäne importieren muss. Gemeinsame Pakete DÜRFEN nur domänenneutrale Infrastruktur wie Transport, Authentisierung, Konfiguration, Logging, Metriken und Build-Hilfen enthalten.

### RPC-ARCH-004 – Optionale hexagonale Runtime-Struktur

Wenn das Generator-Tool hexagonale Runtime-Source erzeugt, MUSS diese Runtime zwischen Domain-Port, Driving-Adaptern und Driven-Adaptern unterscheiden. Transportprofile wie gRPC und TCP-RPC MÜSSEN als Driving-Adapter modelliert werden. PKCS#11-, Netzwerk-HSM-, Cloud-HSM- oder Cloud-KMS-Anbindungen MÜSSEN als Driven-Adapter modelliert werden. Der fachliche Kern DARF keine Transport-, TLS-, mTLS-, Protobuf-Runtime- oder Vendor-SDK-Abhängigkeit benötigen.

### RPC-PRINC-001 – Explizite Semantik

Jede Abweichung von der C-ABI MUSS explizit im Mapping dokumentiert sein.

### RPC-PRINC-002 – Keine versteckte Kryptografie

Der RPC-Server DARF keine kryptografische Operation durch Host-Software ersetzen, wenn die Semantik eine HSM-/PKCS#11-Operation verlangt.

---

## 10. Technologievorgaben

### RPC-TECH-001 – Go

Der MVP-Generator und der MVP-Referenzserver MÜSSEN in Go implementiert werden. Diese Vorgabe beschränkt nicht die Generierung von Server-Stubs für Java, Kotlin und C#.

### RPC-TECH-002 – Protobuf, gRPC und TCP-RPC

Protobuf v3 MUSS als primäre IDL-Technologie verwendet werden. gRPC MUSS als Standardtransportprofil unterstützt werden. TCP-RPC MUSS als zusätzliches Transportprofil unterstützt werden und DARF keine abweichende fachliche PKCS#11-Semantik einführen.

### RPC-TECH-003 – PKCS#11-Backend

Der Go-Referenzserver MUSS ein bestehendes Go-PKCS#11-Binding verwenden, z. B. `github.com/miekg/pkcs11`, sofern keine dokumentierte ADR eine Alternative begründet.

### RPC-TECH-004 – OASIS-Quellen

Der Generator MUSS OASIS-PKCS#11-Header und SOLLTE die Markdown-Spezifikation aus dem OASIS-Repository als Eingabequelle unterstützen.

### RPC-TECH-005 – Cloud-KMS-SDKs

Cloud-KMS-Adapter SOLLEN offizielle Provider-SDKs oder dokumentierte Provider-REST-/gRPC-APIs verwenden. Jede SDK-Abhängigkeit MUSS als eigenes Backend-Modul gekapselt sein und DARF den PKCS#11-Referenzserver nicht zur Pflichtabhängigkeit machen.

### RPC-TECH-006 – Netzwerk-HSM-Client-Libraries

Netzwerk-HSM-Profile SOLLEN über die jeweilige Vendor-PKCS#11-Client-Library angebunden werden, wenn der Hersteller PKCS#11 unterstützt. Native Vendor-Protokolle ohne PKCS#11 SOLLTEN erst nach einem ADR und außerhalb des MVP unterstützt werden.

### RPC-TECH-007 – Gepinnte Generator-Toolchain

Protobuf-Compiler, Sprach-Plugins, Generator-Version und relevante Build-Tools MÜSSEN für reproduzierbare Artefakte versioniert oder gepinnt werden. Ein Generatorlauf MUSS die verwendeten Tool-Versionen in einem Build- oder Quellenmanifest ausgeben.

---

## 11. Umgebungs- und Betriebsanforderungen

### RPC-ENV-001 – Lokale Entwicklung

Das Projekt MUSS eine lokale Entwicklungsumgebung mit SoftHSM v2 bereitstellen oder dokumentieren.

### RPC-ENV-002 – Containerfähigkeit

Der Server MUSS als OCI-Container baubar sein.

### RPC-ENV-003 – Netzwerk-HSM-Betrieb

Für Netzwerk-HSM-Betrieb MUSS das Projekt dokumentieren, welche Dateien, Umgebungsvariablen, Ports, DNS-Namen, Zertifikate und Vendor-Client-Konfigurationen in den Container oder Host eingebunden werden müssen.

### RPC-ENV-004 – Cloud-KMS-Betrieb

Für Cloud-KMS-Profile SOLLTE das Projekt dokumentieren, wie Provider-Credentials, Region/Location, Key-Ressourcen und IAM-/RBAC-Berechtigungen bereitgestellt werden.

### RPC-ENV-005 – Cloud-HSM-Betrieb

Für Cloud-HSM-Profile MUSS das Projekt dokumentieren, welche Cluster-Endpunkte, Client-Libraries, Zertifikate, Trust Anchors, Netzwerkfreigaben, Provider-Identitäten und IAM-/RBAC-Berechtigungen für den Betrieb erforderlich sind.

### RPC-OPS-MON-001 – Prometheus

Metriken SOLLTEN in einem Prometheus-kompatiblen Format exportiert werden.

### RPC-OPS-CFG-001 – Externe Konfiguration

Konfiguration MUSS über Dateien und Umgebungsvariablen möglich sein. Secrets DÜRFEN NICHT ausschließlich über Umgebungsvariablen erzwungen werden.

---

## 12. Compliance

### RPC-COMP-001 – PKCS#11-Spezifikation

Die Abbildung MUSS sich auf eine konkret gepinnte Version der OASIS-PKCS#11-Spezifikation beziehen.

Akzeptanz: Ein Quellenmanifest im Repository dokumentiert Spezifikationsversion, Bezugs-URL, Commit oder Release, Prüfsumme soweit verfügbar und Lizenzhinweis für die verwendeten Header und Spezifikationsauszüge.

### RPC-COMP-002 – Lizenz- und Quellennachweis

Übernommene Header, generierte Artefakte und Spezifikationsauszüge MÜSSEN lizenzkonform dokumentiert werden.

### RPC-COMP-003 – Kryptografische Compliance

Das Projekt DARF keine Aussage treffen, dass der RPC-Server selbst FIPS-zertifiziert sei, nur weil das angebundene HSM FIPS-zertifiziert ist.

### RPC-COMP-004 – Cloud-KMS-Compliance-Grenze

Cloud-KMS-Profile MÜSSEN dokumentieren, welche Compliance-Aussagen vom Provider stammen und welche Aussagen das Projekt selbst trifft. Provider-Zertifizierungen DÜRFEN NICHT automatisch auf den RPC-Server übertragen werden.

### RPC-COMP-005 – Artefakt-Provenienz

Release-Artefakte SOLLEN nachvollziehbar machen, aus welchen OASIS-Quellen, Mapping-Dateien, Generator-Versionen und Protobuf-Toolchains sie erzeugt wurden. Für produktionsfähig deklarierte Releases MUSS diese Provenienz im Repository oder Release-Bundle prüfbar sein.

---

## 13. Bedrohungsmodell

### RPC-THREAT-001 – Kompromittierter Client

Ein kompromittierter Client könnte erlaubte PKCS#11-Operationen missbrauchen. Der Server MUSS Authentisierung, Autorisierung und Auditierung als Gegenmaßnahmen vorsehen.

### RPC-THREAT-002 – Replay von Handles

Ein Angreifer könnte Session- oder Object-Handles wiederverwenden. Handles MÜSSEN an Client-Kontext, Lease und Serverinstanz gebunden sein.

### RPC-THREAT-003 – PIN-Leakage

PINs könnten über Logs, Traces, Panic-Dumps oder Fehlermeldungen abfließen. Der Server MUSS diese Daten maskieren und aus Observability-Pfaden ausschließen.

### RPC-THREAT-004 – Resource Exhaustion

Clients könnten Sessions, Find-Operationen oder Multi-Part-Operationen offen halten. Der Server MUSS Limits, Timeouts und Backpressure bereitstellen.

### RPC-THREAT-005 – Semantikfehler im Generator

Ein falsches Mapping könnte aus Input-Parametern Output-Parameter machen oder sensitive Attribute preisgeben. Generator-Artefakte MÜSSEN durch Golden-Tests und Review abgesichert werden.

### RPC-THREAT-006 – Netzwerk-HSM-Verbindungsabbruch

Netzwerk-HSM-Verbindungen können abbrechen oder in Failover-Zustände wechseln. Der Server MUSS solche Fehler erkennen, Sessions invalidieren und Readiness entsprechend aktualisieren.

### RPC-THREAT-007 – Cloud-KMS-Berechtigungsdrift

Cloud-KMS-Berechtigungen können sich außerhalb des Projekts durch IAM-/RBAC-Änderungen verändern. Cloud-KMS-Profile SOLLTEN Health- und Permission-Checks bereitstellen, ohne produktive Schlüsseloperationen auszuführen.

### RPC-THREAT-008 – Signing-Oracle-Missbrauch

Ein autorisierter oder kompromittierter Client könnte den Server als Signing Oracle missbrauchen. Der Server MUSS Mechanism-, Key- und Client-Policies unterstützen und SOLLTE profilspezifische Limits oder Freigabelisten für besonders kritische Signaturmechanismen bereitstellen.

---

## 14. Risiken

### RPC-RISK-001 – PKCS#11-Komplexität

PKCS#11 enthält viele historisch gewachsene Sonderfälle. Risiko: Der Generator deckt Randfälle nicht korrekt ab.

Mitigation: MVP bewusst auf Kern-API begrenzen, Feature-Matrix führen, Mapping-Datei reviewpflichtig machen.

### RPC-RISK-002 – Vendor-Unterschiede

HSM-Hersteller unterscheiden sich bei Mechanisms, Attributen, Session-Limits und Fehlercodes.

Mitigation: Provider-Profile, Kompatibilitätstests und keine Annahme, dass SoftHSM produktives Verhalten beweist.

### RPC-RISK-003 – Zu starke 1:1-Fixierung

Eine ABI-nahe Abbildung könnte RPC-Modelle unnötig kompliziert machen.

Mitigation: Semantisch 1:1 festschreiben, C-Pointer und Buffer-Konventionen transportgerecht modellieren.

### RPC-RISK-004 – Sicherheitsgrenze wird falsch verstanden

Nutzer könnten annehmen, RPC entferne PKCS#11-Sicherheitsrisiken automatisch.

Mitigation: Dokumentation, Threat Model, sichere Defaults, Autorisierungs- und Auditkonzept.

### RPC-RISK-005 – Cloud-KMS und PKCS#11 werden vermischt

Cloud-KMS ist ressourcenorientiert und zustandslos, PKCS#11 ist session- und handle-orientiert. Risiko: Eine gemeinsame API verwässert beide Modelle.

Mitigation: getrennte Protobuf-Pakete, getrennte Begriffe, keine implizite Cloud-KMS-zu-PKCS#11-Emulation.

### RPC-RISK-006 – Netzwerk-HSM-Latenz und Failover

Netzwerk-HSMs haben andere Latenz-, Timeout- und Failover-Profile als lokale HSMs.

Mitigation: Backend-Profile, Health-Checks, Session-Reconnect-Strategien und produktionsnahe Abnahmetests pro HSM-Profil.

### RPC-RISK-007 – Scope-Creep durch vier Domänen

PKCS#11, Cloud-KMS, Netzwerk-HSM und Cloud-HSM können unterschiedliche Release-Geschwindigkeiten und Betriebsmodelle erzwingen. Risiko: Die MVP-Abnahme wird durch Post-MVP-Domänen blockiert oder eine spätere Domäne übernimmt unpassende Annahmen aus PKCS#11.

Mitigation: klare API-Familien, Profilstatus nach `RPC-LESE-007`, getrennte Abnahmekriterien und domänengetrennte Codepakete.

---

## 15. Annahmen

### RPC-ASSUMP-001 – PKCS#11-Modul verfügbar

Für den Betrieb ist ein PKCS#11-Modul mit erreichbarem Token verfügbar.

### RPC-ASSUMP-002 – Client vertraut Server

Clients vertrauen dem RPC-Server als Policy- und HSM-Zugriffsschicht. Private Schlüssel verlassen das HSM nicht, aber der Server darf Operationen auslösen.

### RPC-ASSUMP-003 – OASIS-Quellen bleiben versionierbar

OASIS-Header und Spezifikationsdokumente sind in konkreten Commits oder Releases referenzierbar.

### RPC-ASSUMP-004 – Nicht-PKCS#11-Domänen sind außerhalb des MVP

Cloud-KMS-, Netzwerk-HSM- und Cloud-HSM-Domänen sind gleichrangige Ziel-Domänen, dürfen aber die Abnahme der PKCS#11-MVP-Kernfunktionalität nicht blockieren, solange sie nicht explizit in einen Release-Scope aufgenommen werden.

### RPC-ASSUMP-005 – PKCS#11-Versions-Baseline

Der MVP nimmt PKCS#11 v3.2 (siehe `RPC-REF-001`) als verbindliche Spezifikations-Baseline an. Header und Spezifikationsdokumente werden in genau dieser Version gepinnt verwendet (`RPC-COMP-001`, `RPC-FA-GEN-004`).

PKCS#11 v2.40 ist kein MVP-Ziel; eine Unterstützung erfolgt – wenn überhaupt – ausschließlich über ein separates, klar gekennzeichnetes Generator-Profil und blockiert die MVP-Abnahme nicht. Vendor-Erweiterungen außerhalb der OASIS-Spezifikation sind durch `RPC-NONGOAL-003` ausgeschlossen, solange sie nicht über einen expliziten Extension-Namespace eingeführt werden.

---

## 16. Abnahmekriterien

### RPC-ACCEPT-001 – Funktionale Abnahme

Ein automatisierter Test MUSS über RPC gegen SoftHSM Slots listen, eine Session öffnen, Login durchführen, einen Key finden und eine Signatur erzeugen. Der Test MUSS erfolgreiche PKCS#11-Operationen mit `CKR_OK` prüfen.

### RPC-ACCEPT-002 – Generator-Abnahme

Ein CI-Job MUSS IDL, Client-Stubs und Server-Stubs aus gepinnten Quellen generieren und gegen Golden Files prüfen. Der Job MUSS fehlschlagen, wenn das Quellenmanifest, die Mapping-Datei oder ein generiertes Artefakt nicht zum eingecheckten Referenzstand passt.

### RPC-ACCEPT-003 – Sprach-Abnahme

Go-, Java-, Kotlin- und C#-Artefakte MÜSSEN mindestens einen Client-Smoke-Test gegen den Go-Referenzserver nachweisen. Zusätzlich MUSS Go den Referenzserver gegen die generierten Server-Stubs bauen; Java, Kotlin und C# MÜSSEN einen kompilierbaren Stub-Harness oder Mock-Server-Kontrakttest gegen die generierten Server-Stubs nachweisen.

### RPC-ACCEPT-004 – Security-Abnahme

Ein Review MUSS bestätigen, dass PINs, HSM-Credentials, private Schlüsselwerte, Secret-Key-Werte und sensitive Attribute nicht geloggt, gemessen, gecacht oder in Fehlerdetails ausgegeben werden. Zusätzlich MUSS mindestens ein automatisierter Negativtest repräsentative Logs, Metriken oder Fehlerdetails auf diese Geheimnisse prüfen.

### RPC-ACCEPT-005 – Betriebsabnahme

Ein lokaler Runbook-Test MUSS den Server gegen SoftHSM starten, Readiness prüfen und eine Demo-Operation ausführen.

### RPC-ACCEPT-006 – Netzwerk-/Cloud-HSM-Profilabnahme

Für jedes als produktionsfähig deklarierte Netzwerk-HSM- oder Cloud-HSM-Profil MUSS ein dokumentierter Smoke-Test Slots listen, eine Session öffnen, einen Login durchführen und eine Signatur oder `C_GenerateRandom` erfolgreich ausführen.

### RPC-ACCEPT-007 – Cloud-KMS-Profilabnahme

Für jedes als produktionsfähig deklarierte Cloud-KMS-Profil MUSS ein dokumentierter Smoke-Test mindestens `ListKeys` oder `GetPublicKey` sowie eine nicht-destruktive Krypto-Operation gegen einen Test-/Sandbox-Key ausführen.

### RPC-ACCEPT-008 – Traceability-Abnahme

Ein Abnahme-Artefakt MUSS alle MVP-blockierenden Anforderungen auf mindestens einen Test, ein Skript, ein Runbook oder einen dokumentierten Review-Beleg abbilden. Anforderungen ohne Beleg DÜRFEN nicht als erfüllt markiert werden.

### RPC-ACCEPT-009 – Secure-Defaults-Abnahme

Ein automatisierter oder reproduzierbarer Starttest MUSS zeigen, dass Profile ihre Transport-Security-Einstellung eindeutig deklarieren. Ein Profil mit aktivierter TLS/mTLS- oder Client-Authentisierungsoption MUSS bei fehlender oder widersprüchlicher Konfiguration fehlschlagen. Profile ohne TLS/mTLS MÜSSEN explizit als solche konfiguriert sein und beim Start sichtbar protokolliert werden. Fehlende Secret-Quellen oder rohe PINs in produktiven Profilen MÜSSEN weiterhin zum Startabbruch führen.

---

## 17. Mengengerüst

### RPC-MENGE-001 – MVP-Umfang API

Der MVP umfasst mindestens 19 PKCS#11-Funktionen aus `RPC-MVP-001`.

### RPC-MENGE-002 – Sessions

Der MVP SOLL mindestens 32 parallele Sessions in der Referenzumgebung verwalten können.

### RPC-MENGE-003 – Stub-Sprachen

Der MVP MUSS vier Zielsprachen abdecken: Go, Java, Kotlin und C#.

### RPC-MENGE-004 – Backend-Profile

Der MVP MUSS mindestens ein SoftHSM-/PKCS#11-Profil enthalten. Netzwerk-HSM-, Cloud-HSM- und Cloud-KMS-Profile sind gleichrangige Ziel-Domänen, aber nicht Teil der MVP-Abnahme.

### RPC-MENGE-005 – Nutzdaten- und Request-Limits

Der MVP MUSS Default-Limits für maximale Request-Größe, maximale Response-Größe, Anzahl paralleler Sessions und Anzahl offener Multi-Part-Operationen dokumentieren. Die Limits MÜSSEN konfigurierbar sein und ihre Überschreitung MUSS deterministisch getestet werden können.

---

## 18. Glossar

### RPC-GLOSS-001 – Begriffe

| Begriff | Bedeutung |
| ------- | --------- |
| ABI | Binary Interface einer nativen Bibliothek; PKCS#11 ist als C-ABI definiert. |
| CK_ATTRIBUTE | PKCS#11-Attribut-Tripel aus Typ, Wertzeiger und Länge. |
| CK_MECHANISM | PKCS#11-Beschreibung eines Mechanismus inklusive optionaler Parameterstruktur. |
| CK_RV | PKCS#11-Returncode (`CK_RETURN_VALUE`) als numerischer Statuswert einer Funktion. |
| Cloud-HSM | Cloud-Dienst, der HSM-Cluster bereitstellt und häufig PKCS#11-Client-Libraries anbietet. |
| Cloud-KMS | Cloud-Dienst mit ressourcenorientierter Key-Management- und Kryptografie-API, meist ohne PKCS#11-Sessions und Handles. |
| Credential-Referenz | Nicht geheimer Verweis auf ein serverseitig konfiguriertes oder extern auflösbares Secret. |
| gRPC | RPC-Framework auf Basis von HTTP/2 und Protobuf; eines der unterstützten Transportprofile. |
| Header-Identität | Client-Identität, die von einem vertrauenswürdigen Proxy oder Mesh über ein konfiguriertes Header-Feld weitergegeben wird. |
| IAM/RBAC | Identity & Access Management bzw. Role-Based Access Control; Berechtigungsmodelle in Cloud- und Plattformsystemen. |
| IDL | Interface Definition Language, hier Protobuf. |
| Identitätsquelle | Konfigurierter Ursprung der Client-Identität, z. B. mTLS-Zertifikat, Proxy-Header oder keine transportseitige Identität. |
| KEM | Key Encapsulation Mechanism; für spätere PQC-Erweiterungen relevant. |
| Lease | Zeitlich begrenzte serverseitige Reservierung einer Ressource (z. B. einer Session) mit Idle-Timeout. |
| Mapping-Datei | Manuell gepflegte Semantik-Ergänzung zu den OASIS-Headern. |
| mTLS | Mutual TLS – beidseitige Authentisierung über X.509-Zertifikate. |
| Netzwerk-HSM | HSM, das über ein Netzwerk und meist über eine Vendor-Client-Library angesprochen wird. |
| OCI | Open Container Initiative; Standard für Container-Images und Runtimes. |
| Opaque Handle | Numerischer Wert, dessen Bedeutung nur der Server kennt. |
| Peer-Allowlist | Liste vertrauenswürdiger Peer-Adressen oder Proxy-/Mesh-Identitäten, die Header-Identitäten setzen dürfen. |
| PKCS#11 | Standardisierte API für kryptografische Tokens und HSMs. |
| Produktionsfähig deklariert | Profilstatus mit dokumentierter Betriebsgrenze, Security-Konfiguration und bestandener domänenspezifischer Abnahme. |
| SBOM | Software Bill of Materials; maschinenlesbare Liste eingesetzter Abhängigkeiten und Lizenzen. |
| Secret-Quelle | Externes System oder lokale Konfiguration, aus der PINs, Tokens, Zertifikate oder Provider-Credentials geladen werden. |
| Semantisch 1:1 | Gleiche fachliche Operation und Fehlersemantik, aber transportgerechte Datentypen statt C-Pointer. |
| Signing Oracle | Missbrauchsmuster, bei dem ein Dienst wiederholt Signaturen für vom Angreifer gewählte Daten erzeugt. |
| TCP-RPC | Eigenes, framing-basiertes RPC-Transportprofil über TCP mit optionalem TLS/mTLS und derselben fachlichen Semantik wie das gRPC-Profil. |

---

## 19. Referenzen

### RPC-REF-001 – Normen und Standards

- OASIS PKCS#11 Specification v3.2 – https://docs.oasis-open.org/pkcs11/pkcs11-spec/v3.2/
- OASIS PKCS#11 GitHub Repository – https://github.com/oasis-tcs/pkcs11
- OASIS PKCS#11 Header (Upstream, im Quellenmanifest zu pinnen) – https://github.com/oasis-tcs/pkcs11/tree/master/working/headers
- OASIS PKCS#11 Specification Markdown (Upstream, im Quellenmanifest zu pinnen) – https://github.com/oasis-tcs/pkcs11/tree/master/working/doc/spec
- AWS KMS API Reference – https://docs.aws.amazon.com/kms/latest/APIReference/
- Google Cloud KMS API Reference – https://cloud.google.com/kms/docs/reference/rest/
- Azure Key Vault Keys REST API – https://learn.microsoft.com/en-us/rest/api/keyvault/keys/
- Protocol Buffers – https://protobuf.dev/
- gRPC – https://grpc.io/

### RPC-REF-002 – Begleitdokumente

- [spec/spezifikation.md](spezifikation.md) – Technische Spezifikation
- [spec/architecture.md](architecture.md) – Architekturüberblick und Komponentensicht
- `docs/compatibility.md` – Kompatibilitätsmatrix (geplant)
- `docs/mapping.md` – Mapping-Regeln (geplant)
