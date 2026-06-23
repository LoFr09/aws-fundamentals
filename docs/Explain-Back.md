# explain_back

## 1. IAM Policy: `ec2-operator` — Verhindern von Terminate

**Welche Zeile (Action/Resource) verhindert das Terminieren?**

Das ganze Statement mit `"Sid": "DenyTermination"`: ein explizites `Deny` auf die Action `ec2:TerminateInstances` (Resource `*`). Ein Deny gewinnt immer, darum kann der User nicht löschen, auch wenn er sonst Rechte hätte.

**Policy-Simulator-Beweis:**
- Welche Zeile verhindert:

            "Sid": "DenyTermination",
            "Effect": "Deny",
            "Action": "ec2:TerminateInstances",
            "Resource": "*"

- Screenshot: ec2-operator terminieren.png

**Welche Zeile müsste ich löschen, damit Terminate erlaubt wäre?**

Achtung, das ist ein Denkfehler, den ich zuerst hatte: Nur das `Deny` zu löschen reicht **nicht**. IAM ist standardmässig „alles verboten, ausser es gibt ein explizites Allow". Meine Policy erlaubt nur Describe, Start und Stop — Terminate steht nirgends als Allow. Entferne ich das `DenyTermination`-Statement, ist Terminate immer noch **implizit** verboten.

Damit Terminate wirklich geht, müsste ich:
1. das `DenyTermination`-Statement entfernen **und**
2. `ec2:TerminateInstances` als `Allow` hinzufügen.

---

## 2. S3-Bucket: nicht öffentlich + `readonly-user` kann nicht schreiben

**Beweis, dass der Bucket nicht öffentlich ist:**
- Im Bucket unter Permissions → Block public access ist „Block all public access" aktiviert (alle vier Schalter an).

**Beweis, dass `readonly-user` nicht schreiben kann:**
- Konkrete Stelle in der Bucket-Policy: das Statement `"Sid": "NurS3User"` — `Effect: Deny`, `Principal: *`, `Action: s3:*`, mit Condition `StringNotLike` auf `aws:PrincipalArn` ungleich dem ARN des `s3-user`. Heisst: jeder, der nicht der s3-user ist, wird für jede S3-Aktion geblockt.
- Exakte Fehlermeldung beim Schreibversuch: `AccessDenied` bei `s3:PutObject`.

**Welche der zwei Schutzschichten würde greifen, wenn die andere fehlte?**

Es gibt zwei Schichten:
- **IAM:** der readonly-user hat über die ReadOnlyAccess-Policy gar kein `PutObject`.
- **Bucket-Policy:** explizites `Deny` für alle ausser dem s3-user.

Beide allein reichen:
- Fehlt die Bucket-Policy → readonly-user kann trotzdem nicht schreiben, weil ihm IAM kein `PutObject` gibt.
- Fehlt die IAM-Beschränkung (User hätte plötzlich Schreibrechte) → die Bucket-Policy blockt ihn trotzdem, weil er nicht der s3-user ist.

Da ein `Deny` immer gewinnt, hält jede Schicht für sich.

---

## 3. CloudTrail-Event

**Mein konkretes Event:**
- Wer (Identity): → aus der Konsole eintragen (ARN der Identität, z.B. lorin-admin)
- Was (Event Name): `StartSession`
- Wann: June 21, 2026, 17:02:47 (UTC+02:00)
- Quelle (Source IP / Service): → Source-IP aus dem Event-Detail eintragen

**„Warum nicht einfach CloudWatch dafür?" — meine Antwort:**

CloudWatch sammelt Betriebs-Metriken und Logs (CPU, Auslastung, Performance) — es sagt mir, *wie* es der Ressource gerade geht, nicht *wer* eine Aktion ausgeführt hat. Den API-Aufruf samt Identität, Zeit und Quelle protokolliert nur CloudTrail.

**Welche Frage kann mein CloudTrail-Event beantworten, die CloudWatch nicht beantwortet?**

„Welcher User hat wann und von welcher IP die Session zur Instanz `i-05f226774a12dbe2f` gestartet?" Das ist eine Wer-hat-was-getan-Frage (Audit), die CloudWatch nicht beantworten kann.

---

## 4. Shared Responsibility Model an einem selbst konfigurierten Dienst

**Gewählter Dienst:** S3 (Bucket `aws-fundamentals-02`)

**Was hat AWS gesichert:**

Hardware, Rechenzentrum und die Haltbarkeit der Daten (Durability). Ausserdem stellt AWS die Verschlüsselungs- und die Policy-Engine bereit.

**Was musste ich sichern:**

Block Public Access aktivieren, Verschlüsselung beim Upload erzwingen (Bucket-Policy verlangt SSE), Zugriff per Bucket-Policy auf den s3-user einschränken und HTTPS erzwingen.

**Was wäre passiert, wenn ich meinen Teil nicht gemacht hätte:**

Der Bucket könnte öffentlich erreichbar sein, Objekte würden unverschlüsselt liegen und fremde Identitäten könnten lesen oder schreiben. AWS schaltet das nichts von selbst ein — das ist mein Teil („IN the Cloud").

---

## 5. Session Manager statt SSH

**Wie geht das technisch ohne offenen Inbound-Port 22?**

Die EC2 hat den SSM-Agent und eine IAM-Role mit `AmazonSSMManagedInstanceCore`. Der Agent baut eine **ausgehende** Verbindung zum SSM-Backend auf. Ich verbinde mich über Konsole/CLI gegen SSM, nicht direkt gegen die Instanz. Es kommt also nie eine Verbindung von aussen rein — Port 22 bleibt zu.

**Warum ist das sicherer als SSH eingeschränkt auf meine IP?**

- Gar kein offener Inbound-Port → keine Angriffsfläche, kein Port-Scanning oder Brute-Force möglich. Bei SSH-auf-meine-IP ist Port 22 trotzdem offen, und meine IP kann wechseln oder gespooft werden.
- Kein SSH-Key, der verloren oder kopiert werden kann — der Zugriff läuft über IAM.
- Voller Audit-Trail: jede Session erscheint in CloudTrail als `StartSession`. Bei klassischem SSH müsste ich auf der Instanz selbst in den Logs nachschauen.

---

## Selbsteinschätzung

| Bereich | Note (A–D) |
|---|---|
| IAM (Policies von Hand schreiben, Roles, Least Privilege, Policy Simulator) | C |
| S3-Sicherheit (Block Public Access, Verschlüsselung, Bucket-Policy) | B |
| Shared Responsibility Model (an konkreten Diensten erklären) | B |
| Auditing (CloudTrail vs. Config vs. CloudWatch, Trusted Advisor vs. Inspector) | A |
| Zugang härten (Session Manager vs. Instance Connect, ohne offenen Port) | A/B |
| Cloud Concepts (Well-Architected, sechs Cloud-Vorteile, Economies of Scale) | B/C |
| CLF-Prüfungsreife (Security and Compliance, Cloud Concepts) | C |


Ich fand diesen Auftrag sehr gut, konnte wirklich viel mitnehmen, was mir auch bei dem CLF-02 helfen wir. Die Policies von Handschreiben geht bei mir meistens schief, aus syntax und konfigurations fehler. Die Log/Monitoring tool konnte ich wicklich vertieft anschauen, nun checke ichs auch. Das Shared Responsibility Model habe ich nun auch besser im griff, die Grauzonen muss ich einfach noch Repetieren. Ich versuche möglichst schnell vortschritt zu machen, das ich auch bald an die Zertifizierung gehen kann.


Ich hatte Hilfe von den verschieden AWS hilfsseiten, ab und zu auch von Claude vorallem bei der verschönerung von Texten und beim Troobleshooting von den Policies.

