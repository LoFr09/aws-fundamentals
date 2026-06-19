# Audit & Monitoring in AWS – CloudTrail, Config, CloudWatch

## (a) CloudTrail aktiviert

Ich habe in meinem Account einen CloudTrail-Trail angelegt, der als Multi-Region-Trail läuft und seine Logs dauerhaft in einen S3-Bucket schreibt. Der Status zeigt "Logging" und die erste Log-Datei wurde bereits geliefert. Damit werden alle Aktivitäten in meinem Konto mitgeschrieben

## (b) Aktion ausgeführt und im Log wiedergefunden

Als nachvollziehbare Aktion habe ich einen IAM-User angelegt und das passende Event in CloudTrail wiedergefunden.

Wichtig dabei und ein Fehler, den ich gemacht hatte: IAM ist ein **globaler Dienst**. CloudTrail schreibt IAM-Events immer in die Region **us-east-1 (N. Virginia)**, egal von wo ich arbeite. In meiner Region (eu-central-1) kam darum "No matches". Erst nach dem Wechsel auf us-east-1 habe ich das Event gefunden.


## (c) Abgrenzung: CloudTrail vs. Config vs. CloudWatch

Alle drei schreiben "irgendwas mit", aber jeder beantwortet eine andere Frage:

**CloudTrail "Wer hat was getan?"**
Protokolliert jede Aktivität in meinem Konto: wer, was, wann, von welcher IP. Es geht um Aktionen und Audit.
Frage, die nur CloudTrail beantwortet: *"Welcher User hat gestern um 14:00 die Security Group geändert?"*

**Config "Wie ist eine Ressource konfiguriert, und war sie mal anders?"**
Hält den Zustand meiner Ressourcen über die Zeit fest und prüft sie gegen Regeln.
Frage, die nur Config beantwortet: *"Hatte mein S3-Bucket letzte Woche irgendwann Public Access aktiviert?"*

**CloudWatch "Wie läuft der Betrieb gerade?"**
Sammelt Metriken, Logs und Alarme zu Performance und Auslastung.
Frage, die nur CloudWatch beantwortet: *"Ist die CPU meiner EC2 in den letzten 10 Minuten über 90 % gestiegen?"*

**Meine Eselsbrücke:**
- CloudTrail = **wer** hat geklickt Aktion
- Config = **wie** sieht es aus / hat es sich verändert Zustand
- CloudWatch = **wie geht's** der Ressource gerade Betrieb





## Trusted Advisor vs Inspector

**Trusted Advisor** Schaut mein ganzes **Konto** an und gibt Empfehlungen zu Kosten, Security, Performance, Fehlertoleranz und Limits. Beispiel: "Diese Security Group ist offen für alle."
 
**Inspector** Scannt meine **Workloads** (EC2, Container) automatisch nach konkreten Schwachstellen . Beispiel: "Auf dieser EC2 läuft eine Version mit bekannter Sicherheitslücke."
 
**Wann was:**
- Überblick, ob mein Konto sauber/günstig/sicher ist → **Trusted Advisor**
- Prüfen, ob auf meinen Servern angreifbare Lücken stecken → **Inspector**

