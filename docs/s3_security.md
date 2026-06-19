# Verschlüsselung, Bucket-Policy, Block Public Access 


## Block Public Access



**1. new ACLs**

Verhindert, dass neue öffentliche ACLs angelegt werden. Was schon öffentlich ist, bleibt es. Schützt also nur vor künftigen Fehlern.

---

**2. any ACLs**

Ignoriert alle öffentlichen ACLs, auch die, die schon da sind. Strenger als Nr. 1.

---

**3. new policies**

Verhindert neue Bucket-Policies, die öffentlichen Zugriff geben würden. Bestehende Policies wirken weiter.

---

**4. any policies**

Ignoriert alle öffentlichen Policies, auch bestehende, und zusätzlich den Zugriff aus anderen AWS-Konten (cross-account). Die strengste Option.

---


## Server-Side Encryption 

**SSE-S3**

S3 verwaltet den Schlüssel komplett selbst. Du hast keine Kontrolle, kein Audit-Log, aber dafür keine Zusatzkosten ausser dem Speicherplatz. Standard und einfach.

---

**SSE-KMS**

Der Schlüssel liegt im Dienst AWS KMS. Du bekommst mehr Kontrolle (eigene Schlüssel, IAM-Rechte) und vor allem ein Audit-Log: jede Schlüsselnutzung wird in CloudTrail protokolliert. Kostet dafür extra (KMS-Gebühren). 

---


## Bucket-Policy


```JSON
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "NurS3User",
      "Effect": "Deny",
      "Principal": "*",
      "Action": "s3:*",
      "Resource": [
        "arn:aws:s3:::aws-fundamentals-02",
        "arn:aws:s3:::aws-fundamentals-02/*"
      ],
      "Condition": {
        "StringNotLike": {
          "aws:PrincipalArn": "arn:aws:iam::236353235096:user/s3-user"
        }
      }
    },
    {
      "Sid": "NurHTTPS",
      "Effect": "Deny",
      "Principal": "*",
      "Action": "s3:*",
      "Resource": [
        "arn:aws:s3:::aws-fundamentals-02",
        "arn:aws:s3:::aws-fundamentals-02/*"
      ],
      "Condition": {
        "Bool": {
          "aws:SecureTransport": "false"
        }
      }
    },
    {
      "Sid": "NurVerschluesselteUploads",
      "Effect": "Deny",
      "Principal": "*",
      "Action": "s3:PutObject",
      "Resource": "arn:aws:s3:::aws-fundamentals-02/*",
      "Condition": {
        "Null": {
          "s3:x-amz-server-side-encryption": "true"
        }
      }
    }
  ]
}
```
*Habe es zuerst selbst versucht, musste dan aber Hilfe von KI haben, das kann ich noch nicht so.*



## Test datei hochgeladen

Ich habe mit dem s3-user eine Testdatei (test.txt) per CLI in den Bucket aws-fundamentals-02 hochgeladen und dabei mit --sse AES256 die Verschlüsselung gesetzt.
Danach habe ich mit aws s3 api head-object die Eigenschaften des Objekts abgefragt.
In der Ausgabe steht "ServerSideEncryption": "AES256", also liegt die Datei verschlüsselt at rest.
Ich habe es über die CLI gemacht, da es nicht immer funktioniert hat über die Konsole und es so meistens besser funktioniert laut AWS.



## Test versuch mit Readonly-User


Ich habe mit dem readonly-user bewusst versucht, eine Datei in den Bucket aws-fundamentals-02 zu schreiben.
Der Versuch ist mit AccessDenied gescheitert: Der User darf s3:PutObject nicht ausführen, blockiert durch ein explizites Deny in der Bucket-Policy. 
Damit ist nachgewiesen, dass nur der s3-user schreiben darf und alle anderen abgelehnt werden.



## Shared Responsibility Modell

Dieses Beispiel zeigt das Shared Responsibility Model in der Praxis: AWS stellt die Verschlüsselung und die Policy-Engine bereit, aber aktiviert wird nichts von AWS. Ich musste die Verschlüsselung beim Upload selbst setzen und über die Bucket-Policy erzwingen, und ich musste den Zugriff selbst auf den s3-user einschränken. AWS liefert also die Tools, aber die richtige Konfiguration liegt bei mir.









https://docs.aws.amazon.com/AmazonS3/latest/userguide/access-control-block-public-access.html

https://docs.aws.amazon.com/AmazonS3/latest/userguide/serv-side-encryption.html