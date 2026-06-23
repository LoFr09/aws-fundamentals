# Probe Prüfung 22.06.2026

Ich habe heute 2 verschiedene Probeprüfungen gelösst, einmal die die ich schon letztes mal gelösst habe und noch eine andere Version.

## b Auswertung

### Probeprüfung Version 1

**Technology** 72% richtig

**Security and Compliance** 60 % Richtig

**Billing and Pricing** 70 % Richtig

**Cloud Concepts** 75 % Richtig

Gesammt 70 % richtig = Bestanden

### Probeprüfung 2

**Technology** 64 % richtig

**Security and Compliance** 53 % Richtig

**Billing and Pricing** 56 % Richtig

**Cloud Concepts** 50 % Richtig

Gesammt 56 % richtig = noch nicht bestanden



## Vergleich

## Vergleich Probeprüfungen

| Bereich | Probeprüfung01 (Versuch 1) | PrüfungV1 (Versuch 2) | PrüfungV2 (Versuch 1) |
|---|---|---|---|
| **Technology** | 15/29 = 52 % | 21/29 = 72 % | 14/22 = 64 % |
| **Security and Compliance** | 3/10 = 30 % | 6/10 = 60 % | 8/15 = 53 % |
| **Billing and Pricing** | 7/10 = 70 % | 7/10 = 70 % | 5/10 = 50 % |
| **Cloud Concepts** | 7/16 = 44 % | 12/16 = 75 % | 10/18 = 56 % |
| **TOTAL** | **32/65 = 49 %** | **46/65 = 70 %** | **37/65 = 56 %** |
| Status | Nicht bestanden | Bestanden | Nicht bestanden |
| Dauer | 44 Min | 44 Min | 50 Min |

Man sieht eine starke Verbesserung zwischen durchlauf 1 und 2 von Probeprüfung 1, dies könnte jedoch auch sein das ich die Frage schon kannte.


# Nacharbeit: Meine 2 schwächsten Gebiete

## Security & Compliance

### Frage 1 (Mock V1 Versuch 2, F43)

**Frage:** Eine Firma hat eine hybride Cloud-Architektur (EC2 + on-premises Server) und will die Server-Logs zentral sammeln. Welcher Ansatz ist am effektivsten?

- **Richtige Antwort:** Amazon CloudWatch Logs mit dem CloudWatch Agent für beide Quellen 
- **Warum richtig (eigene Worte):** CloudWactch Logs ist Amazons Log-Sammelstelle. Der CloudWatch Agent kann auf jeder Linux/Windows-Maschine installiert werden, egal ob das eine EC2-Instanz oder ein on-prem Server in deinem Keller ist. Der Agent schiebt die Logs in einen zentralen CloudWatc-Bereich. So hast du eine Anlaufstellefür alle Log, kannst suchen, filtern und Alerts setzen.

- **Warum mein Fehler ein Fehler war:** 
Ich habe an CloudTrail gedacht, weil "Logs" für mich nach CloudTrail angehört hatte. Aber CloudTrail loggt AWS-API.Aufrufe (wer hat was gemacht), nicht Server-Logs. Dumme verwechslung, die ich nun nicht mehr mache.

---

### Frage 2 (Mock V2 Versuch 1, F12)

**Frage:** Was ist im AWS Shared Responsibility Model eine geteilte Verantwortung zwischen AWS und Kunde?

- **Richtige Antwort:** Patch Management, Configuration Managemant und Awarness/Training
- **Warum richtig (eigene Worte):** Das sind Graubereiche: 
    - Patch Management: AWS patcht den Hypervisor und bei Managed Services das Gast-OS, aber der User pacht das eigene OS selbst.
    -  Configuration Management: AWS konfiguriert die Basis-Infrastruktur, der User konfiguriert seinen VPC, Securtity Groups und Apps
    - Awarness/Training: AWS schult deren Leute, der User seine.

- **Warum mein Fehler ein Fehler war:** 
Ich war mir nicht sicher und musste Raten
---

### Frage 3 (Mock V2 Versuch 1, F26)

**Frage:** Eine Firma will volle Kontrolle über die Erstellung und Nutzung eigener Schlüssel für Verschlüsselung in AWS-Services. Welche Option erfüllt das?

- **Richtige Antwort:** AWS CloudHSM
- **Warum richtig (eigene Worte):** Es gibt 3 Schlüssel Typen: AWS owned keys, AWS managed key Customer managed keys, Für volle Kontrolle über Erstellung und Nutzen bruchst du Customer-managed keys. Wennman noch mehr Kontrolle brucht kann man CloudHSM benutzen.
- **Warum mein Fehler ein Fehler war:** 
Ich habe AWS managed keys gewählt, weil ich dachte das bietet am meisten Kontrolle.
---

## Billing & Pricing

### Frage 4 (Mock V1 Versuch 3, F57)

**Frage:** Welcher AWS-Service kann verwendet werden, um die AWS-Account-Nutzung und Kosten vorherzusagen (Forecast)?

- **Richtige Antwort:** AWS Cost Explorer
- **Warum richtig (eigene Worte):** Cost Explorer hat einen eingebauten Forecast Mechanismus: er nimmt die bisherigen Ausgaben und rechnet Trends und Saisonalität rein, und schaut bis 12 Monate in die zukunft.
- **Warum mein Fehler ein Fehler war:** 
Ich habe es verwechselt mit AWS Cost and Usage Report.
---

### Frage 5 (Mock V1 Versuch 3, F18)

**Frage:** Welcher AWS-Service analysiert deine Infrastruktur und identifiziert ungenutzte oder underutilized Amazon EBS Elastic Volumes?

- **Richtige Antwort:** Trust Advisor
- **Warum richtig (eigene Worte):** Trust Advisor von AWS, er rennt durch deine Ressourcen und sagt: Hier ein EBS Volume das seit 30 Tagen an keiner Instanz hängt, wirfs weg oder mach was damit. 
- **Warum mein Fehler ein Fehler war:** 
Ich habe config gewählt, weil ich dachte die Resource konfig wir getrackt
---

### Frage 6 (Mock V2 Versuch 1, F41)

**Frage:** Ein DevOps-Team verschiebt 500 GB Daten von einer EC2-Instanz zu einem S3-Bucket in derselben Region. Welche Kosten fallen für diesen Daten-Transfer an?

- **Richtige Antwort:** Der Transfer ist kostenlos
- **Warum richtig (eigene Worte):** Innerhalb derselben Region zwischen AWS-Services ist Daten Transfer i vielen Fällen Gratis
- **Warum mein Fehler ein Fehler war:** 
Ich dachte eingehender verkehr ist gartis der Rest kostet.
---

## Mein Fazit aus der Nacharbeit

Ich habe zu viel geraten wenn ich nicht sicher war, deshalb hatte ich auch noch vieles Falsch, ich nehme mit, das ich die basics noch besser anschaue das die Sitzen und dan kann ich daraus ableiten. Ich fühle mich noch nicht Prüfungsreif, da ich eindeutig noch zu viele Fehler mache.