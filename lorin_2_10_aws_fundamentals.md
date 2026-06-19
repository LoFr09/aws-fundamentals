# Aufgabe: AWS Fundamentals — Security, IAM und Cloud-Konzepte

**Block:** 2.10 — AWS Fundamentals — Security, IAM und Cloud-Konzepte
**KI-Regel:** 📖 Manual First (Schritte 1–6) / 💡 KI als Hinweisgeber (Schritt 7)
**Markierung:** LAB / ZERTIFIZIERUNG
**Voraussetzung:** Block 2.6 (AWS-Account, IAM-User, EC2, VPC) ist abgeschlossen — du arbeitest auf demselben Account weiter. Grundverständnis Linux, SSH, Berechtigungen. Dieser Block läuft parallel zu deiner AWS-Cloud-Practitioner-Vorbereitung (CLF-C02, SwissSkills Skill 53 / AWS Academy) und ist darauf abgestimmt.

**Zeit:** 20h

---

### Szenario

Du machst parallel die AWS Certified Cloud Practitioner Prüfung (CLF-C02). Deine erste Probeprüfung (65 Fragen) liegt ausgewertet vor — und sie zeigt ein klares Bild: **Security and Compliance** hast du nur zu 30% richtig beantwortet, **Cloud Concepts** zu 44%. Genau diese beiden Gebiete sind auf CLF-C02 am höchsten gewichtet (Security and Compliance allein ca. 30% der Prüfung). Billing/Pricing (70%) und Technology (52%) sind in Ordnung.

In der Probeprüfung sind dir konkrete Dienste durcheinandergeraten: CloudTrail, Config und CloudWatch; Trusted Advisor und Inspector; EC2 Instance Connect und Systems Manager Session Manager; Elastic Beanstalk und CloudFormation. Das sind keine Wissenslücken die du wegliest — das sind Dienste die du anfassen musst, bis der Unterschied sitzt.

Dieser Block schliesst die Lücke nicht mit Theorie, sondern mit Hands-on. Du baust in deinem AWS-Account die Security-Bausteine selbst auf, dokumentierst sie an deinen eigenen Artefakten und gehst am Ende mit einer zweiten Probeprüfung in den Soll-Ist-Vergleich. Das Ziel: nach diesem Block bist du auf den prüfungsrelevanten Security-Themen nicht nur belesen, sondern handlungssicher.

---

### Auftrag

Vertiefe in deinem bestehenden AWS-Account (Block 2.6) die Themen IAM, S3-Sicherheit, Shared Responsibility, Auditing und das Well-Architected Framework — durchgängig praktisch. Schreibe eigene IAM-Policies und prüfe sie im Policy Simulator, sichere einen S3-Bucket mit Verschlüsselung und Bucket-Policy ab, deklinierst die geteilte Verantwortung an konkreten Diensten durch, aktivierst CloudTrail und weist ein Event nach, ersetzt offenen SSH-Zugang durch Session Manager und gehst deine 2.6-Umgebung entlang der sechs Well-Architected-Säulen durch. Zum Abschluss machst du eine CLF-Probeprüfung, wertest sie nach Themenbereich aus und arbeitest die zwei schwächsten Gebiete dokumentiert nach. Jeder Schritt wird so dokumentiert, dass dein BB an deinen eigenen Artefakten nachvollziehen kann, dass du es verstanden hast — nicht nur gelesen.

---

### Schritte

| # | Was | Deliverable | Zeit |
|---|-----|-------------|------|
| 1 | **IAM vertiefen: Policies, Roles, Least Privilege** — In Block 2.6 hast du einen IAM-Admin-User erstellt. Jetzt gehst du eine Ebene tiefer. (a) Erstelle drei IAM-User mit unterschiedlichem Bedarf: `readonly-user` (darf nur lesen), `s3-user` (darf nur mit einem bestimmten S3-Bucket arbeiten), `ec2-operator` (darf EC2 starten/stoppen, aber nicht terminieren). (b) Schreibe für jeden eine **eigene Policy von Hand im JSON-Editor** — nicht nur AWS-managed Policies anklicken. Wende konsequent Least Privilege an: nur die Aktionen, die der User wirklich braucht, eingeschränkt auf die konkreten Ressourcen (ARNs). (c) Erstelle eine **IAM-Role** für eine EC2-Instanz, die dieser Instanz S3-Lesezugriff gibt — ohne dass Access Keys auf der Instanz liegen. Erkläre, warum eine Role hier besser ist als ein User mit Access Key. (d) Prüfe jede deiner Policies im **IAM Policy Simulator**: Simuliere für `ec2-operator` die Aktion `ec2:TerminateInstances` und zeige, dass sie verweigert wird. Dokumentiere Eingabe und Ergebnis des Simulators als Screenshot. (e) Erkläre den Unterschied zwischen einer identity-based Policy (am User/Role) und einer resource-based Policy (am Bucket) | `docs/iam_vertiefung.md` — drei Policies als JSON, Role-Erklärung, Policy-Simulator-Screenshots (verweigert + erlaubt), Unterschied identity- vs. resource-based | 4h |
| 2 | **S3 absichern: Verschlüsselung, Bucket-Policy, Block Public Access** — (a) Erstelle einen S3-Bucket. Aktiviere **Block Public Access** auf Bucket-Ebene und erkläre, was die vier einzelnen Einstellungen jeweils blockieren. (b) Aktiviere **Server-Side Encryption** (SSE-S3 oder SSE-KMS) und erkläre den Unterschied zwischen den beiden. (c) Schreibe eine **Bucket-Policy**, die nur dem `s3-user` aus Schritt 1 Zugriff gibt und alle unverschlüsselten Uploads ablehnt. Zwei getrennte Conditions: (1) `aws:SecureTransport: false` → Deny — erzwingt HTTPS für den Transport. (2) Deny bei fehlendem `s3:x-amz-server-side-encryption`-Header — erzwingt Encryption at rest. Die beiden sind komplementär, nicht austauschbar. (d) Lade eine Testdatei hoch und weise nach, dass sie verschlüsselt at rest liegt (Objekt-Eigenschaften). (e) Versuche bewusst, mit dem `readonly-user` in den Bucket zu schreiben — der Versuch muss scheitern. Dokumentiere die Fehlermeldung. Diese geteilte Verantwortung — AWS stellt die Verschlüsselungs-Funktion, DU musst sie aktivieren und die Policy richtig setzen — ist genau das, was die Prüfung im Shared Responsibility Model abfragt | `docs/s3_security.md` — Bucket-Konfiguration, Bucket-Policy als JSON, Nachweis Verschlüsselung, dokumentierter Fehlversuch readonly-user | 2.5h |
| 3 | **Shared Responsibility an konkreten Diensten durchdeklinieren** — Das Shared Responsibility Model ist das am höchsten gewichtete Prüfungsthema und gleichzeitig dein schwächstes. Theorie reicht hier nicht — du gehst es an realen Diensten durch. Erstelle eine Tabelle mit den drei Diensttypen **EC2 (IaaS), RDS (managed, aus Block 2.9) und S3 (managed Storage)**. Für jeden Dienst trägst du konkret ein: Was sichert/verantwortet AWS (z.B. Hypervisor, physische Hardware, Patches der Managed-Engine)? Was musst DU sichern (z.B. Gast-OS-Patches bei EC2, Security Groups, IAM, Bucket-Policies, Datenklassifizierung)? Beziehe dich dabei auf das, was du in Schritt 1, 2 und in Block 2.6/2.9 tatsächlich selbst konfiguriert hast — nenne die konkrete Einstellung, nicht nur die Kategorie. Ergänze: Warum verschiebt sich die Linie zwischen IaaS, PaaS und SaaS? Was bedeutet "Security OF the Cloud" vs. "Security IN the Cloud"? | `docs/shared_responsibility.md` — Tabelle EC2/RDS/S3 mit konkreten Einstellungen aus den eigenen Schritten, IaaS/PaaS/SaaS-Linie, OF vs. IN | 2h |
| 4 | **Auditing: CloudTrail aktivieren und ein Event nachweisen — und die Dienste sauber abgrenzen** — In der Probeprüfung sind dir CloudTrail, Config und CloudWatch durcheinandergeraten. Diesen Schritt nutzt du, um den Unterschied an der laufenden Umgebung festzunageln. (a) Aktiviere **CloudTrail** in deinem Account (Trail anlegen, in einen S3-Bucket schreiben lassen). (b) Führe eine nachvollziehbare Aktion aus (z.B. einen IAM-User erstellen oder eine Security-Group-Regel ändern) und **finde dieses konkrete Event im CloudTrail-Log wieder** — wer (Identity), was (Event Name), wann, von welcher IP. Dokumentiere den Log-Eintrag. (c) Schreibe eine klare Abgrenzung in eigenen Worten: **CloudTrail** (wer hat was getan — API-Aufrufe/Audit) vs. **Config** (wie ist eine Ressource konfiguriert / Compliance über Zeit) vs. **CloudWatch** (Metriken, Logs, Alarme zur Performance/Betrieb). Für jeden Dienst eine konkrete Frage, die nur er beantworten kann. (d) Ebenfalls abgrenzen, weil prüfungsrelevant: **Trusted Advisor** (Account-weite Empfehlungen zu Kosten/Security/Performance/Limits) vs. **Inspector** (automatisiertes Vulnerability-Assessment von Workloads/EC2). Wofür würdest du welchen einsetzen? | `docs/auditing.md` — CloudTrail aktiv, ein wiedergefundenes Event dokumentiert, Abgrenzung CloudTrail/Config/CloudWatch, Abgrenzung Trusted Advisor/Inspector | 3h |
| 5 | **Zugang härten: Session Manager statt offenem SSH-Port** — In Block 2.8 war ein offener Port 22 das Kernthema. In AWS gibt es einen Weg, ganz ohne offenen SSH-Port auf eine Instanz zu kommen. (a) Verbinde dich mit deiner EC2-Instanz über **AWS Systems Manager Session Manager** — ohne Port 22 in der Security Group zu öffnen. Voraussetzung: SSM-Agent (bei Amazon Linux 2023 vorinstalliert) und eine IAM-Role mit der passenden Managed Policy an der Instanz. (b) Entferne (oder hattest nie) die SSH-Inbound-Regel und weise nach, dass du trotzdem auf der Instanz arbeiten kannst. (c) Grenze ab: **EC2 Instance Connect** (browserbasiert, nutzt SSH/Port 22 im Hintergrund, temporärer Key) vs. **Session Manager** (kein offener Port, läuft über das SSM-Backend, voll auditierbar via CloudTrail). Wann nimmst du welchen? (d) Verbinde zurück zu Schritt 4: Eine Session-Manager-Sitzung taucht in CloudTrail auf — finde den entsprechenden Eintrag | `docs/session_manager.md` — Session-Manager-Verbindung ohne Port 22 (Screenshot), Security Group ohne SSH-Regel als Nachweis, Abgrenzung Instance Connect vs. Session Manager, CloudTrail-Eintrag der Session | 2.5h |
| 6 | **Well-Architected Framework an der eigenen 2.6-Umgebung** — Cloud Concepts war dein zweitschwächstes Gebiet. Das Well-Architected Framework ist hier der Kern. Statt die sechs Säulen abstrakt zu lernen, gehst du deine **bestehende 2.6-Umgebung** (VPC, EC2, Security Groups, ggf. RDS aus 2.9) entlang der Säulen durch: **Operational Excellence, Security, Reliability, Performance Efficiency, Cost Optimization, Sustainability**. Pro Säule: (a) Was erfüllt deine Umgebung heute schon? (b) Wo ist eine konkrete Schwäche? (c) Eine konkrete Verbesserung, die du benennen (nicht zwingend umsetzen) kannst. Ergänze einen kurzen Theorieteil zu zwei Konzepten, die in der Probeprüfung gefragt waren: die **sechs Vorteile von Cloud Computing** (laut AWS) und **Economies of Scale** — jeweils mit einem Bezug zum Treuhand-Kunden aus Block 2.6 | `docs/well_architected.md` — sechs Säulen an der eigenen Umgebung (Ist/Schwäche/Verbesserung), sechs Cloud-Vorteile, Economies of Scale mit Kundenbezug | 2h |
| 7 | **CLF-Prüfungsreife: Probeprüfung, Auswertung, gezielte Nacharbeit** — (a) Mache eine vollständige **CLF-C02-Practice-Exam-Runde** (AWS Academy / offizieller AWS-Übungstest / Skill-53-Trainingsmaterial). (b) Werte dein Ergebnis **nach Themenbereich** aus: Cloud Concepts, Security and Compliance, Technology and Services, Billing/Pricing — Prozent pro Gebiet, wie bei der ersten Probeprüfung. (c) Vergleiche mit dem Ausgangsstand (Security 30%, Cloud Concepts 44%) — wo stehst du jetzt? (d) Nimm die **zwei schwächsten Gebiete** aus dieser Runde und arbeite sie dokumentiert nach: pro Gebiet drei Fragen, die du falsch hattest, mit der richtigen Antwort UND einer Erklärung in eigenen Worten, warum die richtig ist und warum dein Fehler ein Fehler war. (e) Kurzfazit: Fühlst du dich prüfungsreif, oder welche Gebiete brauchen noch eine Runde? KI als Hinweisgeber ist hier erlaubt, um Erklärungen zu Konzepten zu bekommen — aber die Auswertung deines eigenen Ergebnisses und die "warum war das mein Fehler"-Reflexion schreibst du selbst | `docs/clf_pruefungsreife.md` — Ergebnis nach Themenbereich, Vergleich mit Ausgangsstand, zwei schwächste Gebiete mit je drei nachgearbeiteten Fragen, Prüfungsreife-Fazit | 2h |

---

### Erwartetes Ergebnis

```
aws-fundamentals/
├── docs/
│   ├── iam_vertiefung.md          ← drei Hand-Policies, Role, Policy-Simulator-Nachweis
│   ├── s3_security.md             ← Bucket-Policy, Verschlüsselung, Block Public Access
│   ├── shared_responsibility.md   ← EC2/RDS/S3 mit konkreten eigenen Einstellungen
│   ├── auditing.md                ← CloudTrail-Event, Abgrenzung CloudTrail/Config/CloudWatch + TA/Inspector
│   ├── session_manager.md         ← Zugang ohne Port 22, Abgrenzung Instance Connect/SSM
│   ├── well_architected.md        ← sechs Säulen an eigener Umgebung, Cloud-Vorteile
│   ├── clf_pruefungsreife.md      ← Probeprüfung ausgewertet, Nacharbeit, Fazit
│   └── explain_back.md            ← Antworten auf Explain-Back-Fragen
├── policies/
│   ├── readonly-user.json         ← eigene IAM-Policy
│   ├── s3-user.json               ← eigene IAM-Policy
│   ├── ec2-operator.json          ← eigene IAM-Policy
│   └── bucket-policy.json         ← S3 Bucket-Policy
└── screenshots/
    ├── policy_simulator_deny.png  ← TerminateInstances verweigert
    ├── policy_simulator_allow.png ← erlaubte Aktion
    ├── s3_encryption.png          ← Objekt verschlüsselt at rest
    ├── cloudtrail_event.png       ← wiedergefundenes Event
    └── session_manager.png        ← Verbindung ohne offenen Port 22
```

IAM ist vertieft: drei eigene Least-Privilege-Policies geschrieben und im Simulator verifiziert, eine Role statt Access Keys verstanden. Ein S3-Bucket ist abgesichert (Block Public Access, Verschlüsselung, Bucket-Policy), der unautorisierte Zugriff scheitert nachweislich. Shared Responsibility ist an den eigenen Diensten durchdekliniert. CloudTrail läuft und ein konkretes Event ist wiedergefunden, die Dienste CloudTrail/Config/CloudWatch und Trusted Advisor/Inspector sind sauber abgegrenzt. Der EC2-Zugang läuft über Session Manager ohne offenen Port 22. Die eigene Umgebung ist entlang der sechs Well-Architected-Säulen bewertet. Eine zweite CLF-Probeprüfung ist ausgewertet und die schwächsten Gebiete sind dokumentiert nachgearbeitet.

---

### Bewertungskriterien

| Kriterium | Gewicht |
|---|---|
| IAM vertieft: drei eigene Hand-Policies mit Least Privilege, Role statt Access Key, Policy-Simulator-Nachweis (deny + allow) | 20% |
| S3 abgesichert: Block Public Access, Verschlüsselung at rest nachgewiesen, Bucket-Policy funktioniert, Fehlversuch dokumentiert | 15% |
| Shared Responsibility an EC2/RDS/S3 mit konkreten eigenen Einstellungen durchdekliniert (nicht abstrakt) | 15% |
| CloudTrail aktiv, ein Event wiedergefunden, Abgrenzung CloudTrail/Config/CloudWatch + Trusted Advisor/Inspector korrekt | 20% |
| Session Manager ohne Port 22 funktioniert, Abgrenzung Instance Connect/SSM, CloudTrail-Bezug | 15% |
| Well-Architected an eigener Umgebung + CLF-Probeprüfung ausgewertet und schwächste Gebiete nachgearbeitet | 15% |

---

### Leistungsziele (WISS)

- **B3.1** — Sicherheitssituation in Bezug auf System, Netzwerk, Software und Daten klären (Shared Responsibility: was sichert AWS, was der Kunde)
- **B3.3** — Nötige und empfohlene Schutzmassnahmen vorschlagen (IAM Least Privilege, S3-Verschlüsselung, Auditing)
- **C3.1** — Schutzwürdige Daten identifizieren und kategorisieren (Datenklassifizierung im Shared-Responsibility-Teil)
- **C3.3** — Erforderliche Schutzmechanismen gemäss Schutzwürdigkeit abklären und qualifizieren (Verschlüsselung, Block Public Access)
- **C3.4** — Datensicherheits- und Rollenkonzept erarbeiten (IAM-User, Policies, Roles, Least Privilege)
- **C3.6** — Zugriffsberechtigungen gemäss Konzept setzen (IAM-Policies, Bucket-Policy)
- **C3.7** — Daten gemäss Konzept verschlüsseln (S3 Server-Side Encryption)
- **C3.8** — Eingerichtete Schutzmechanismen auf Wirksamkeit überprüfen (Policy Simulator, dokumentierter Fehlversuch, CloudTrail-Nachweis)
- **E4.1** — Sicherheitsrisiken anhand aktueller Tools analysieren und bewerten (Trusted Advisor, Inspector)
- **F1.4** — Geeignete Plattformen und Leistungsparameter anhand messbarer Kriterien bestimmen (Well-Architected, Service-Abgrenzung)
- **F6.1** — Sicherheitskonzept für Serversysteme und -dienste erstellen (Auditing- und Zugriffskonzept in der Cloud)
- **F6.2** — Sicherheitselemente effektiv konfigurieren (IAM, Bucket-Policy, Session Manager, CloudTrail)
- **F6.3** — Sicherheitssysteme testen und Ergebnisse nachvollziehbar dokumentieren (Policy Simulator, Event-Nachweis, Fehlversuch)
- **F6.4** — Sicherheitskonzepte auf Aktualität prüfen und an technologische Entwicklungen anpassen (CVE-Awareness, Inspector)

---

### Ressourcen

> Diese Quellen sind deine Hilfsmittel — nutze sie aktiv! Lies sie bevor du anfängst. Bei Problemen: zuerst hier nachschlagen, dann Stuck-Protokoll, dann erst BB fragen.

| Quelle | Link / Befehl |
|---|---|
| AWS Cloud Practitioner (CLF-C02) Prüfungsleitfaden | https://aws.amazon.com/certification/certified-cloud-practitioner/ |
| AWS Skill Builder — Cloud Practitioner (kostenlos) | https://skillbuilder.aws/ |
| AWS Well-Architected Framework | https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html |
| AWS Shared Responsibility Model | https://aws.amazon.com/compliance/shared-responsibility-model/ |
| IAM Best Practices | https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html |
| IAM Policy Simulator | https://policysim.aws.amazon.com/ |
| IAM Policies und Permissions | https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html |
| IAM Roles für EC2 | https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-ec2.html |
| S3 Block Public Access | https://docs.aws.amazon.com/AmazonS3/latest/userguide/access-control-block-public-access.html |
| S3 Server-Side Encryption | https://docs.aws.amazon.com/AmazonS3/latest/userguide/serv-side-encryption.html |
| S3 Bucket Policies | https://docs.aws.amazon.com/AmazonS3/latest/userguide/bucket-policies.html |
| AWS CloudTrail User Guide | https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html |
| AWS Config | https://docs.aws.amazon.com/config/latest/developerguide/WhatIsConfig.html |
| CloudWatch vs. CloudTrail vs. Config (AWS Doku) | https://docs.aws.amazon.com/cloudtrail/latest/userguide/cloudtrail-and-cloudwatch.html |
| AWS Trusted Advisor | https://docs.aws.amazon.com/awssupport/latest/user/trusted-advisor.html |
| Amazon Inspector | https://docs.aws.amazon.com/inspector/latest/user/what-is-inspector.html |
| Systems Manager Session Manager | https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager.html |
| EC2 Instance Connect | https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Connect-using-EC2-Instance-Connect.html |
| AWS Free Tier | https://aws.amazon.com/free/ |
| AWS Preisrechner | https://calculator.aws/ |

---

### Kosten-Hinweis (Free Tier)

> Du arbeitest auf demselben Account wie in Block 2.6. IAM, Policies, Bucket-Policies, CloudTrail (erster Trail) und Session Manager sind kostenlos bzw. im Free Tier. **Achtung bei:** CloudTrail-Logs landen in einem S3-Bucket (Storage-Kosten gering, aber vorhanden), Config kann Kosten verursachen (Config in diesem Block nur erklären, nicht zwingend dauerhaft aktivieren), Inspector hat Kosten ausserhalb der Trial. **Disziplin wie in 2.6:** EC2-Instanzen nach der Arbeit stoppen, nicht laufen lassen. Ungebrauchte Test-Buckets und Test-User am Ende aufräumen. Den Billing-Alert aus Block 2.6 ($5) prüfst du am Ende dieses Blocks einmal.

---

### Git-Disziplin

> Wie in Block 2.8 und 2.9: Eigenes Git-Repo für diesen Block, **ein Commit pro Schritt** mit aussagekräftiger Commit-Message. Die `.json`-Policies und die `docs/`-Files gehören versioniert. Screenshots können im Repo liegen oder separat abgelegt und in den Docs referenziert werden. Bei der Abgabe schaut der BB auch in die Git-History — eine saubere, schrittweise History zeigt, dass du strukturiert gearbeitet hast (das hast du bei 2.8/2.9 gut gemacht, halte es).

---

### Stuck-Protokoll

> **Bevor du den BB fragst, hast du diese 4 Schritte gemacht:**

| # | Schritt | Check |
|---|---------|-------|
| 1 | **Fehlermeldung gelesen** — nicht nur gesehen, sondern verstanden was sie sagt | [ ] |
| 2 | **In Doku gesucht** — mindestens 1 konkreten Link oder Doku-Stelle notiert | [ ] |
| 3 | **Mindestens 1 Lösungsversuch** gemacht und das Ergebnis dokumentiert | [ ] |
| 4 | **Aufgeschrieben:** *"Ich wollte X, hab Y probiert, es passiert Z"* | [ ] |

**"Ich weiss nicht" ist keine Frage.**
**"Ich hab X probiert und komme bei Y nicht weiter" ist eine.**

> Tipp: Diese 4 Schritte sind exakt das was du in der IPA auch machen musst — da steht niemand daneben. Wer das hier trainiert, hat in der IPA einen Riesenvorteil.

> **Zeitregel:** Arbeite mindestens 45 Minuten an einem Problem bevor du zum BB gehst. Die meisten Probleme lösen sich durch Doku lesen und Ausprobieren.

---

### Explain-Back

> **Nach Abschluss:** Beantworte diese Fragen schriftlich in `docs/explain_back.md`. Kurze, klare Antworten in eigenen Worten — kein Copy-Paste aus dem Internet, keine KI-Prosa. Dein BB schaut sich die Antworten an wenn er Zeit hat. Drei dieser Fragen kannst du nur an deinen eigenen Artefakten beantworten — genau das ist der Punkt.

1. **Zeig an deiner `ec2-operator`-Policy**, an welcher konkreten Zeile (Action und/oder Resource) verhindert wird, dass dieser User eine Instanz terminieren darf. Beweise es mit deinem Policy-Simulator-Screenshot: welche Eingabe hast du gemacht, und was war das exakte Ergebnis? Wenn du eine Zeile aus der Policy löschen würdest, sodass Terminate plötzlich erlaubt wäre — welche?
2. **Beweise, dass dein S3-Bucket nicht öffentlich ist** und dass der `readonly-user` nicht hineinschreiben kann. Nenne die konkrete Einstellung bzw. die konkrete Stelle in deiner Bucket-Policy, die das jeweils bewirkt, und die exakte Fehlermeldung, die beim Schreibversuch kam. Welche der beiden Schutzschichten würde greifen, wenn die andere fehlte?
3. Du hast in CloudTrail ein Event wiedergefunden. **Nenne von deinem konkreten Event:** wer (Identity), was (Event Name), wann, von welcher Quelle. Wenn jemand fragt "Warum nicht einfach CloudWatch dafür?" — was antwortest du, und welche Frage könntest du mit deinem CloudTrail-Event beantworten, die CloudWatch dir nicht beantwortet?
4. Der Kunde fragt: *"Unsere Daten liegen jetzt in AWS — ist AWS für die Sicherheit verantwortlich?"* Erkläre das Shared Responsibility Model an einem Dienst, den du in diesem Block selbst konfiguriert hast. Was hat AWS gesichert, was hast DU sichern müssen — und was wäre passiert, wenn du deinen Teil nicht gemacht hättest?
5. Du verbindest dich per Session Manager auf eine EC2-Instanz, ohne dass Port 22 offen ist. Erkläre, wie das technisch geht (warum braucht es keinen offenen Inbound-Port?) und warum das sicherer ist als eine SSH-Regel, selbst wenn die SSH-Regel nur auf deine IP eingeschränkt wäre.

---

### Selbsteinschätzung

> Schätze dich nach Abschluss selbst ein: **A** = sicher/selbstständig, **B** = machbar mit gelegentlicher Hilfe, **C** = unsicher in der Praxis, **D** = Thema noch nicht geläufig

| Bereich | Note (A-D) |
|---------|------------|
| IAM (Policies von Hand schreiben, Roles, Least Privilege, Policy Simulator) | _____ |
| S3-Sicherheit (Block Public Access, Verschlüsselung, Bucket-Policy) | _____ |
| Shared Responsibility Model (an konkreten Diensten erklären) | _____ |
| Auditing (CloudTrail vs. Config vs. CloudWatch, Trusted Advisor vs. Inspector) | _____ |
| Zugang härten (Session Manager vs. Instance Connect, ohne offenen Port) | _____ |
| Cloud Concepts (Well-Architected, sechs Cloud-Vorteile, Economies of Scale) | _____ |
| CLF-Prüfungsreife (Security and Compliance, Cloud Concepts) | _____ |
