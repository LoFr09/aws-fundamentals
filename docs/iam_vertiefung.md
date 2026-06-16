# IAM Vertiefung

## Policies

### readonly-user
AWS-managed Policy **ReadOnlyAccess** direkt an den User gehängt.
Sie ist fast 3000 Zeilen lang, darum nur der Link:
https://docs.aws.amazon.com/aws-managed-policy/latest/reference/ReadOnlyAccess.html

### s3-user
Darf alles auf S3, aber nur auf dem Bucket `bucket-for-s3-user`.

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "s3:*",
            "Resource": [
                "arn:aws:s3:::bucket-for-s3-user",
                "arn:aws:s3:::bucket-for-s3-user/*"
            ]
        },
        {
            "Effect": "Deny",
            "NotAction": "s3:*",
            "NotResource": [
                "arn:aws:s3:::bucket-for-s3-user",
                "arn:aws:s3:::bucket-for-s3-user/*"
            ]
        }
    ]
}
```

Der Deny-Teil sperrt alles ab, was nicht S3 ist und nicht zu diesem Bucket gehört.

### ec2-operator
Darf starten und stoppen, aber nicht löschen. `DescribeInstances` ist nötig, damit man die Instances sieht.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowDescribe",
      "Effect": "Allow",
      "Action": "ec2:DescribeInstances",
      "Resource": "*"
    },
    {
      "Sid": "AllowStartStop",
      "Effect": "Allow",
      "Action": ["ec2:StartInstances", "ec2:StopInstances"],
      "Resource": "*"
    },
    {
      "Sid": "DenyTermination",
      "Effect": "Deny",
      "Action": "ec2:TerminateInstances",
      "Resource": "*"
    }
  ]
}
```

`DescribeInstances` geht nur mit `Resource: "*"`, das lässt sich nicht einschränken.

## Wieso macht eine Rolle mehr Sinn?

Mit einem User müsste ich einen Access Key erstellen und auf der Instance speichern. Der liegt dann dauerhaft dort und ist ein Sicherheitsrisiko.
Eine Rolle gibt der Instance automatisch temporäre Zugangsdaten, die AWS selber erneuert. Es liegt kein Schlüssel auf der Instance.
Darum ist die Rolle sicherer und macht weniger Arbeit.

## Tests

**ec2-operator:** Starten und Stoppen hat funktioniert, Löschen wurde verweigert. Am Anfang habe ich vergessen, Read (`DescribeInstances`) zu geben, darum habe ich die Instances nicht gesehen. Nach dem Ergänzen ging es.


**readonly-user:** Instance starten ging nicht (verweigert). Alle Buckets sehen ging. Also: lesen ja, ändern nein.

**s3-user:** Über die CLI getestet (kein Konsolen-Zugriff), dafür Access Key erstellt. Datei in den Bucket schreiben ging, alles andere wurde abgelehnt.

## Unterschied identity-based vs. resource-based Policy

- **Identitätsbasiert:** hängt am User, an einer Gruppe oder Rolle. Sagt, was diese Identität darf. Beispiel: meine `s3-only`-Policy am User `s3-user`.
- **Ressourcenbasiert:** hängt an der Ressource (z.B. Bucket Policy am S3-Bucket). Sagt, wer auf diese Ressource zugreifen darf.

Beide werden zusammen geprüft:
- Ein **Deny** gewinnt immer.
- Kein Deny + mindestens ein **Allow** = erlaubt.
- Nirgends ein Allow = automatisch verweigert.

Mehr dazu:
https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_identity-vs-resource.html



```
Mit Claude den Text verbessert / sauberer dargestellt
```
