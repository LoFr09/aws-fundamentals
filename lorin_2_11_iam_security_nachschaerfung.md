# Aufgabe: IAM & Security — Nachschärfung zu Block 2.10

**Block:** 2.11 — IAM & Security Nachschärfung (Vertiefung zu 2.10)
**KI-Regel:** 📖 Manual First — die Erklärungen schreibst du selbst, in eigenen Worten. KI ist hier ausdrücklich kein Hilfsmittel: Es geht genau um die Stellen, an denen in 2.10 KI im Spiel war oder ein Nachweis fehlte. KI darf erst nach deiner Abgabe zur Kontrolle dienen.
**Markierung:** LAB / NACHSCHÄRFUNG
**Voraussetzung:** Block 2.10 ist abgegeben. Du arbeitest auf **derselben Umgebung und im selben Repo** weiter — deine 2.10-Policies, dein Bucket, deine `ec2-operator`-Policy. Nichts wird neu erfunden, alles am eigenen Bestand nachgeschärft.
**Zeit:** 6h

---

### Szenario

Deine 2.10-Abgabe war solide — die Git-Disziplin sass, die Praxis-Stolpersteine (CloudTrail global nach us-east-1, der SSM-Agent-Neustart) hast du selbst gelöst und sauber erklärt. Drei Dinge sind aber nicht ganz zugemacht, und genau die entscheiden, ob IAM wirklich sitzt:

- Der **Policy-Simulator-Nachweis** fehlte — du hast das Terminieren über die Live-Konsole bewiesen statt über den Simulator, der in 2.10 Pflicht war.
- Für `readonly-user` hast du die **AWS-managed `ReadOnlyAccess`** angehängt statt eine eigene Policy zu schreiben — die Aufgabe verlangte drei Hand-Policies, geliefert hast du zwei.
- Beim **Shared Responsibility Model** bist du auf der Kategorie-Ebene geblieben („Bucket-Policy setzen") statt deine konkrete Einstellung zu nennen.

Dazu zwei Verständnis-Stellen: die **Bucket-Policy** ist die technisch stärkste Arbeit in 2.10 — aber sie ist mit KI-Hilfe entstanden, also musst du sie jetzt selbst erklären können. Und bei **CloudHSM** (CLF-Nacharbeit) hast du „mehr Kontrolle" als Grund genannt — das ist nicht der richtige Trigger.

Dieser Block ist keine Strafe und keine Wiederholung von vorn. Er nimmt deine eigene 2.10-Arbeit und schliesst die offenen Stellen — damit dein BB an deinen eigenen Artefakten sieht, dass du es nicht nur gebaut, sondern verstanden hast.

---

### Auftrag

Geh an deine bestehende 2.10-Umgebung zurück und schliesse fünf Stellen: liefere die fehlenden Policy-Simulator-Nachweise, ersetze die managed `ReadOnlyAccess` durch eine eigene Hand-Policy, schärfe die geteilte Verantwortung an deiner konkreten Konfiguration nach, erkläre deine eigene Bucket-Policy Zeile für Zeile, und korrigiere deine CloudHSM-Einordnung. Jede Erklärung bezieht sich auf deine realen Artefakte — Policy-Namen, Bucket-Name, Statement-Sid — nicht auf allgemeine Beispiele.

---

### Schritte

| # | Was | Deliverable | Zeit |
|---|-----|-------------|------|
| 1 | **Policy-Simulator-Nachweis nachliefern** — Nimm deine bestehende `ec2-operator`-Policy und prüfe sie im **IAM Policy Simulator**. Liefere zwei Screenshots: `ec2:StartInstances` → **allowed**, `ec2:TerminateInstances` → **denied**. In 2.10 hast du das Terminieren über die Live-Konsole verweigert bekommen — das ist ein gültiger Praxis-Beleg, aber nicht der geforderte Nachweis. Erkläre zusätzlich schriftlich: Warum ist der Simulator dem Live-Test als Erstprüfung überlegen? Was kann beim Live-Terminieren-Versuch schiefgehen, wenn die Policy in Wahrheit zu weit wäre? | Zwei Simulator-Screenshots (allow/deny), schriftliche Begründung Simulator vs. Live-Test | 1h |
| 2 | **Dritte Hand-Policy: `readonly-user` umstellen** — In 2.10 trägt `readonly-user` die managed `ReadOnlyAccess`. Ersetze sie durch eine **eigene, von Hand geschriebene** identity-based Policy. `ReadOnlyAccess` deckt hunderte Services ab — das ist das Gegenteil von Least Privilege. Entscheide bewusst, welche Lese-/Describe-Actions der Auftrag „darf nur lesen, was läuft" wirklich verlangt (z.B. EC2 beschreiben, S3 auflisten/lesen, CloudWatch-Metriken lesen) und begründe deine Auswahl. Weise im Simulator nach: eine deiner erlaubten Lese-Actions → allowed, eine schreibende Action (z.B. `s3:PutObject`) → denied. | `policies/readonly-user.json` (Hand-Policy), Begründung der Action-Auswahl, Simulator-Nachweis allow/deny | 1.5h |
| 3 | **Shared Responsibility an deiner konkreten Konfiguration** — Nimm **einen** deiner real konfigurierten Dienste aus 2.10 — am besten deinen S3-Bucket `aws-fundamentals-02`. Beschreibe die geteilte Verantwortung nicht auf Kategorie-Ebene, sondern an deiner echten Einstellung: Was sichert AWS (Durability, Hardware, die Policy-/Verschlüsselungs-Engine)? Und was hast **du** konkret gesichert — mit Nennung der konkreten Stelle: das Statement `NurS3User` in deiner Bucket-Policy, der aktivierte Block Public Access (welche der vier Schalter), die erzwungene SSE. Schreib es so, dass jemand deine Bucket-Policy aufmachen und jede Aussage darin wiederfinden kann. | `docs/shared_responsibility_konkret.md` — geteilte Verantwortung an `aws-fundamentals-02` mit benannten konkreten Einstellungen | 1h |
| 4 | **Bucket-Policy Zeile für Zeile erklären** — Deine 2.10-Bucket-Policy ist technisch die beste Arbeit im Block, aber mit KI-Hilfe entstanden. Jetzt erklärst du sie Statement für Statement in eigenen Worten: Was tut `NurS3User` (Deny für alle ausser dem `s3-user` über die `StringNotLike`-Condition auf `aws:PrincipalArn`)? Was tut das HTTPS-Statement (`aws:SecureTransport`)? Und im Detail die **`Null`-Condition** beim Verschlüsselungs-Header: Was bedeutet `"Null": {"s3:x-amz-server-side-encryption": "true"}` genau — auf welchen Fall trifft sie zu, und was passiert bei einem Upload, dem dieser Header fehlt? Wenn du eine dieser Zeilen nicht selbst erklären kannst, ist das das Signal, sie nachzuarbeiten — nicht abzuschreiben. | `docs/bucket_policy_erklaert.md` — jedes Statement deiner eigenen Bucket-Policy in eigenen Worten, Schwerpunkt `Null`-Condition | 1h |
| 5 | **CloudHSM korrigieren** — In deiner 2.10-CLF-Nacharbeit hast du CloudHSM mit „mehr Kontrolle" begründet. Das trifft die Abgrenzung nicht. Erkläre neu und sauber: Wann reicht **KMS**, und wann ist **CloudHSM** zwingend? Der echte Trigger ist nicht ein gradueller „mehr Kontrolle"-Unterschied, sondern ein **dediziertes Single-Tenant-HSM mit alleiniger Schlüssel-Custody** — wenn Regulatorik ein dediziertes Gerät verlangt, das nicht auf einem geteilten AWS-Backend liegen darf. Aktueller Stand, den du kennen musst: AWS KMS ist seit Februar 2025 **selbst FIPS 140-3 Security Level 3** zertifiziert — die alte Faustregel „CloudHSM braucht man für Level 3" stimmt damit nicht mehr. Grenze ausserdem SSE-S3, SSE-KMS und CloudHSM in einem Satz pro Variante ab. | `docs/kms_cloudhsm_korrektur.md` — KMS-vs-CloudHSM mit echtem Trigger, FIPS-Currency-Hinweis, Abgrenzung der drei Verschlüsselungswege | 1.5h |

---

### Erwartetes Ergebnis

```
aws-fundamentals/                  ← dein bestehendes 2.10-Repo, erweitert
├── policies/
│   └── readonly-user.json         ← NEU: Hand-Policy statt managed ReadOnlyAccess
├── docs/
│   ├── shared_responsibility_konkret.md
│   ├── bucket_policy_erklaert.md
│   └── kms_cloudhsm_korrektur.md
└── screenshots/
    ├── simulator_start_allow.png
    ├── simulator_terminate_deny.png
    └── simulator_readonly_*.png
```

Wie in 2.10: **ein Commit pro Schritt** mit aussagekräftiger Message.

---

### Explain-Back

Schriftlich, in eigenen Worten, kein Copy-Paste, keine KI-Prosa. Die Fragen beziehen sich auf **deine** Artefakte — allgemeine Antworten zählen nicht.

1. Zeig an deiner `ec2-operator`-Policy die konkrete Stelle, die das Terminieren verhindert. Greift bei dir ein **impliziter** oder ein **expliziter** Deny? Wenn du eine einzige Änderung machen wolltest, sodass Terminate erlaubt wäre — welche genau, und reicht eine?
2. Deine alte `readonly-user`-Lösung (`ReadOnlyAccess`) und deine neue Hand-Policy verbieten beide das Schreiben. Warum ist die Hand-Policy trotzdem die bessere Lösung? Nenne eine konkrete Berechtigung, die `ReadOnlyAccess` mitbringt und deine Hand-Policy bewusst nicht.
3. Erkläre die `Null`-Condition in deiner Bucket-Policy so, als würdest du sie einem anderen Lernenden beibringen: Welcher Upload wird geblockt, welcher nicht, und warum braucht es dafür `Null` und nicht einfach einen Vergleich auf den Header-Wert?
4. Ein Kunde sagt: „Ich will volle Kontrolle über meine Schlüssel, nimm CloudHSM." Wann ist das die richtige Antwort und wann übertrieben? Mach den Unterschied an einem konkreten Kriterium fest — nicht an „mehr Kontrolle".

---

### Bewertung

Dieser Block schliesst zusammen mit 2.10 das vollständige Bild. Bewertet wird, ob die **offenen Stellen aus 2.10 echt geschlossen** sind: der Simulator-Nachweis liegt vor, `readonly-user` trägt eine eigene Hand-Policy (managed Policy gilt weiter als nicht erfüllt), die geteilte Verantwortung ist an benannten konkreten Einstellungen festgemacht, und — am wichtigsten — die Bucket-Policy-Erklärung und die CloudHSM-Korrektur zeigen Verständnis in eigenen Worten, nicht wiedergekäute KI-Prosa. Die Explain-Back-Antworten an den eigenen Artefakten sind der Kern der Bewertung.
