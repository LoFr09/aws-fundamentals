
# Zugriff auf EC2 ohne offenen Port – Session Manager

## (a) Verbindung über Session Manager

Ich habe mich über den AWS Systems Manager Session Manager mit meiner EC2-Instanz (`test01`, Amazon Linux 2023) verbunden **ohne Port 22 zu öffnen und ohne SSH-Key**.

Damit das geht, brauchte es zwei Dinge:
- Den **SSM-Agent** auf der Instanz.
- Eine **IAM-Role** mit der Managed Policy `AmazonSSMManagedInstanceCore`, die an die Instanz geknüpft ist.

Stolperstein, den ich dabei gelöst habe: Anfangs war keine IAM-Role dran, darum war der Ping-Status "Offline" und der Agent meldete "no valid credentials". Nach dem Anhängen der Rolle musste ich die Instanz noch neu starten, damit der Agent die neuen Credentials aufgreift. Danach wurde die Instanz "Online" und ich konnte verbinden.

Beweis, dass ich wirklich drauf bin:

Siehe Screenshot: Verbunden via SSM

## (b) Keine SSH-Regel nötig



Trotzdem läuft meine Session weiter. Der Grund: Session Manager braucht keinen offenen Inbound-Port. Der SSM-Agent baut die Verbindung *ausgehend* zum SSM-Backend auf. Es kommt also nie eine Verbindung von aussen auf die Instanz zu, darum ist Port 22 komplett unnötig.

## (c) EC2 Instance Connect vs. Session Manager

| | EC2 Instance Connect | Session Manager |
|---|---|---|
| Wie | Browserbasiert, nutzt im Hintergrund SSH über Port 22 | Läuft über das SSM-Backend, kein offener Port |
| Key | Schiebt einen temporären SSH-Key auf die Instanz | Gar kein SSH-Key nötig |
| Port 22 | Muss offen sein | Muss nicht offen sein |
| Audit | – | Voll auditierbar über CloudTrail |

**Wann was:**
- Schnell mal per Browser auf eine Instanz, bei der Port 22 ohnehin offen ist = EC2 Instance Connect.
- Sicherer Zugriff ganz ohne offenen Port, mit sauberem Audit-Trail = Session Manager (Ich würde immer mich so verbinden in einem laufenden betrieb).

## (d) Session in CloudTrail wiederfinden

Eine Session-Manager-Sitzung taucht in CloudTrail als Event `StartSession` auf.

Mein gefundener Eintrag:

| Frage | Wert |
|---|---|
| Event name | `StartSession` |
| Wann (Event time) | June 21, 2026, 17:02:47 (UTC+02:00) |
| Ziel-Instanz | `i-05f226774a12dbe2f` |

Damit schliesst sich der Kreis zu CloudTrail: Auch der Zugriff selbst ist nachvollziehbar, im Gegensatz zu klassischem SSH, wo ich auf der Instanz nachschauen muesste.



