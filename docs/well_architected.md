# Well-Architected Framework an meiner 2.6-Umgebung

Meine Umgebung: eigener AWS-Account (Root + IAM), eigene VPC (10.0.0.0/16) mit Public- und Private-Subnet, EC2 (t3.micro, Nginx) mit Security Group. Kunde: Müller Treuhand AG (18 Mitarbeitende).

## Die sechs Säulen

### 1. Operational Excellence (sauberer Betrieb)
- **Gut:** Ich dokumentiere jeden Schritt, habe einen Billing-Alarm bei 5 USD eingerichtet und treffe bewusste Entscheidungen.
- **Schwäche:** Alles ist manuell in der Konsole zusammengeklickt, VPC, Subnets, Route Tables, EC2, Security Group. Nicht wiederholbar.
- **Besser:** Die ganze Umgebung mit Terraform als Code bauen.

### 2. Security (Daten und Zugriff schützen)
- **Gut:** Root hat MFA und wird nicht im Alltag genutzt, IAM-User über Gruppe mit MFA, SSH nur von meiner IP (/32) und nur per Key, das Private-Subnet hat keine Internet-Route.
- **Schwäche:** Mein lorin-admin hat AdministratorAccess – mehr als für den Alltag nötig. Und der Webserver läuft nur über HTTP, ohne TLS.
- **Besser:** HTTPS/TLS für Nginx und engere IAM-Rechte statt voller Admin-Rechte.

### 3. Reliability (Ausfallsicherheit)
- **Gut:** Saubere Trennung in Public- und Private-Subnet über zwei AZs (1a/1b).
- **Schwäche:** Es läuft nur eine einzige EC2-Instanz in einer AZ – fällt die aus, ist alles weg. Kein Backup/Snapshot, kein Load Balancer.
- **Besser:** EC2 in eine Auto Scaling Group über beide AZs, davor ein Load Balancer, plus regelmässige Snapshots/AMI.

### 4. Performance Efficiency (passende Grösse)
- **Gut:** Ich habe einen kleinen, passenden Instanz-Typ gewählt.
- **Schwäche:** Ich messe die Auslastung nicht, ich weiss gar nicht, ob t3.micro reicht oder zu gross ist.
- **Besser:** Mit CloudWatch die CPU/RAM-Auslastung beobachten und die Grösse bei Bedarf anpassen.

### 5. Cost Optimization (keine unnötigen Kosten)
- **Gut:** Billing-Alarm bei 5 USD, Free Tier bewusst genutzt, Test-Instanz nach der Prüfung terminiert, NAT Gateway.
- **Schwäche:** Läuft die Nginx-Instanz dauerhaft, fallen nach dem Free Tier ca. 8.70 $/Monat an – ohne automatische Abschaltung.
- **Besser:** Instanz stoppen wenn ungenutzt, bei Dauerbetrieb auf einen Savings Plan wechseln.

### 6. Sustainability (ressourcenschonend)
- **Gut:** Kleine, bedarfsgerechte Instanz statt überdimensioniert, Test-Ressourcen terminiert, kein unnötiges NAT Gateway.
- **Schwäche:** Eine dauerlaufende, kaum ausgelastete Instanz ist Leerlauf und damit Energieverschwendung.
- **Besser:** Richtig dimensionieren und Ungenutztes konsequent abschalten.

## Theorie

### Sechs Vorteile von Cloud Computing (laut AWS)
1. **Investition gegen variable Kosten tauschen**, nur zahlen, was ich nutze.
2. **Economies of Scale nutzen**, durch viele Kunden zusammen günstiger.
3. **Kapazität nicht mehr raten**, nach Bedarf hoch/runter skalieren.
4. **Schneller und flexibler**, Ressourcen in Minuten statt Wochen.
5. **Keine eigenen Rechenzentren**, AWS kümmert sich um die Hardware.
6. **In Minuten weltweit**, mit wenigen Klicks in anderen Regionen.

**Müller Treuhand AG:** Sie braucht keinen eigenen Server-Raum und keine grosse Vorab-Investition, bekommt gute Infrastruktur günstig, kann zur Steuersaison hoch- und danach runterskalieren und ist schnell startklar. Genau das zeigt mein Kostenvergleich: On-Premise ~CHF 82'400 über 5 Jahre gegen ~CHF 49'700 in der Cloud.

### Economies of Scale
Je grösser die Menge, desto günstiger das Einzelne. AWS bündelt die Nutzung von hunderttausenden Kunden und gibt die niedrigeren Kosten als günstige Pay-as-you-go-Preise weiter.

**Müller Treuhand AG:** Die kleine Firma bekommt dieselbe sichere, redundante Infrastruktur wie ein Grosskonzern, zu einem Preis, den sie mit einem einzelnen eigenen Server nie erreichen würde.

---
Quelle: https://docs.aws.amazon.com/whitepapers/latest/aws-overview/six-advantages-of-cloud-computing.html

*Hatte hilfe von KI bei der Unterteilung der einzelnen Komponenten*