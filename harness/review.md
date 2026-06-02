# Review Harness

Diese Datei standardisiert Reviews fuer `crypto-rpc`. Review beantwortet:
Welche Risiken, Vertragsbrueche oder Wartbarkeitsprobleme enthaelt ein
Diff, bevor Verification die DoD-Closure prueft?

Review ist eine Entscheidungsvorlage. Reviewer implementieren nicht,
verifizieren nicht vollstaendig gegen DoD und validieren nicht den
realen Nutzerbedarf.

## Input Context

Ein reproduzierbarer Review braucht mindestens:

| Quelle | Zweck |
| --- | --- |
| Diff oder PR-Patch | Was wurde geaendert |
| Aktiver Slice oder Plan | Scope, DoD, explizite Nicht-Ziele |
| Betroffene `RPC-*`-IDs | Produktvertrag und Belegtyp aus `RPC-LESE-002` |
| Betroffene ADRs | Architektur- und Toolentscheidungen |
| [`AGENTS.md`](../AGENTS.md) | Hard Rules |
| [`harness/roles.md`](roles.md) | Rollenabgrenzung |
| [`harness/verification.md`](verification.md) | Abgrenzung zu Verification |
| Relevante Sensors oder fehlende Sensors | Welche Evidence existiert oder fehlt |

Ohne Plan- oder Spec-Kontext ist ein Review nur Code- oder Doku-Kritik,
kein Harness-Review.

## Finding Categories

Findings werden absteigend sortiert: HIGH vor MEDIUM vor LOW vor INFO.

| Kategorie | Bedeutung | Blockiert |
| --- | --- | --- |
| HIGH | Produktvertrag, Sicherheit, Datenintegritaet, Architekturgrenze oder Evidence-Vertrag kann brechen | ja |
| MEDIUM | Wahrscheinliche Regression, fehlender Test, unklare Traceability oder relevantes Drift-Risiko | normalerweise vor Merge klaeren |
| LOW | Wartbarkeit, Doku-Praezision oder kleine Konsistenzluecke ohne unmittelbaren Vertragsbruch | nein |
| INFO | Beobachtung oder Kontext ohne Aenderungspflicht | nein |

### HIGH-Anker fuer `crypto-rpc`

Ein Finding ist HIGH, wenn es eines dieser Muster trifft:

- Eine `RPC-*`-Anforderung, ein MVP-Abnahmekriterium oder ein Belegtyp
  aus `RPC-LESE-002` kann verletzt werden.
- Spezifikation, ADR oder Code schraenkt das Lastenheft ein.
- Die vier gleichrangigen Domaenen werden vermischt, z. B. Cloud-KMS
  wird still als PKCS#11-Profil modelliert.
- `CK_RV`-Treue oder die Trennung fachlicher Returncodes von
  Transportfehlern wird gebrochen.
- PINs, Klartexte, Schluesselmaterial oder Sensitive Attributes koennen
  in Logs, Traces, Audits, Goldens oder Fehlermeldungen landen.
- Generatoren oder eingecheckte Artefakte koennen ohne Golden-/Replay-
  Evidence driften.
- Der Proto-Quellpfad-Konflikt `spec/proto/` vs. top-level `proto/`
  wird ohne Folge-ADR entschieden.
- SoftHSM-Evidence wird als Produktionsfaehigkeit ausgegeben.
- Ein geplantes, aber nicht existierendes Gate wird als ausgefuehrte
  Evidence behauptet.
- Eine Accepted ADR wird inhaltlich umgeschrieben statt durch Folge-ADR
  geschaerft.

### MEDIUM-Anker fuer `crypto-rpc`

Ein Finding ist MEDIUM, wenn es eines dieser Muster trifft:

- Neuer oeffentlicher API-, Generator- oder Profilpfad hat keinen
  negativen Test- oder Golden-Plan.
- Eine Aenderung nennt keine passende `RPC-*`-, ADR- oder Plan-ID.
- Doku/README/CHANGELOG driftet gegen Lastenheft, Spezifikation oder
  Roadmap.
- Ein Plan-Eintrag benennt die betroffene Domaene nicht, obwohl der
  Scope domaenenspezifisch ist.
- Toolchain-, Header-, Mapping- oder Generator-Versionen sind nicht
  gepinnt oder nicht als spaeterer Pin-Punkt dokumentiert.
- Java/Kotlin/C#-Stub-Harness wird mit vollstaendigen Referenzservern
  verwechselt oder umgekehrt.
- Ein LOW-Muster wiederholt sich und wird zum Drift-Signal.

## Review Lenses

Reviewer pruefen diese Linsen explizit und berichten auch
Negativbefunde fuer relevante Linsen.

| Linse | Prueffrage |
| --- | --- |
| Spec / Traceability | Sind `RPC-*`-/ADR-/Plan-Anker korrekt, vollstaendig und testbar? |
| Lastenheft vs. Spezifikation | Bleibt das Lastenheft ungeschmaelert? |
| Domaenenmodell | Bleiben PKCS#11, Cloud-KMS, Netzwerk-HSM und Cloud-HSM sauber getrennt? |
| MVP-Scope | Wird M1 nicht still ueber- oder unterdefiniert? |
| Returncode / Transport | Bleibt `CK_RV` fachlich und Transportfehler transportbezogen? |
| Security / Secrets | Bleiben PINs, Klartexte und Sensitive Attributes aus Logs, Traces, Goldens? |
| Generator / Replay | Gibt es Goldens fuer Fresh-State, Idempotenz und Safety-Pfade? |
| Proto / IDL | Ist der kanonische Proto-Pfad ADR-konform oder bewusst offen gehalten? |
| Docker-only / Gates | Werden nur real existierende Sensors behauptet? |
| Docs / Release | Sind README, ADR-Index, Roadmap, CHANGELOG und Planning konsistent? |

## Output Schema

Jedes Finding verwendet dieses Schema:

```text
<CATEGORY> <path>:<line> - <kurzer Titel>
Quelle: <RPC-*|ADR-*|Hard Rule|Maintainability>
Befund: <1-2 beobachtbare Saetze>
Risiko: <warum das relevant ist>
Verifizierbar: <Sensor/Test/Review-only>
```

Am Ende jedes Reviews:

```text
Geprueft ohne Befund:
- <Linse oder Pfad>

Nicht geprueft:
- <Linse oder Pfad> - <Grund>
```

## Non-Goals

- Keine Implementierungsvorschlaege als Ersatz fuer Findings.
- Keine Refactors ausserhalb des Diff-Scopes.
- Keine DoD-Closure. Das ist Verifier-Aufgabe.
- Keine Validation gegen Nutzerbedarf. Das ist Validator-Aufgabe.
- Keine Abwertung von Findings, weil ihre Behebung unbequem ist.

## Steering Loop

Wenn dasselbe Finding dreimal auftritt:

1. Klassifikation in dieser Datei schaerfen.
2. Pruefen, ob `AGENTS.md`, ADR, Spezifikation oder Lastenheft eine Hard
   Rule braucht.
3. Pruefen, ob ein computational Sensor moeglich ist, sobald der
   Build-Harness existiert.
4. Falls es nur temporaer toleriert wird: Plan-Anker oder Folge-Slice
   dokumentieren.
