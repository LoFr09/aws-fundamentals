# Shared Responsibility – S3-Bucket `aws-fundamentals-02`
 
## Was ich mir merke
AWS baut das Schloss. Abschliessen muss ich.
 
## Was AWS sichert
- Hardware, Rechenzentren, Strom, physische Sicherheit
- **Durability**: Objekte werden automatisch über mehrere Availability Zones repliziert ohne das Ich was machen muss
- Die **Engines**: dass AES-256 / KMS korrekt verschlüsselt und dass meine Policy (Allow/Deny) zuverlässig ausgewertet wird
---

## Was ich sichere
 
**1. Zugriff, Statement `NurS3User` in der Bucket-Policy**
Legt fest, wer auf den Bucket darf. Also heisst, ich muss zb die User Policy schreiben oder auswählen.

**2. Block Public Access**
Verhindert, dass der Bucket öffentlich wird. Die vier Schalter:
- `BlockPublicAcls`
- `IgnorePublicAcls`
- `BlockPublicPolicy`
- `RestrictPublicBuckets`
Ich musste ja in 2.10 alle 4 auswählen damit man von aussen nicht drauf kommt, meines wissens ist das auch beste Practis im Sicherheitsbreich

**3. Erzwungene Verschlüsselung (SSE)**
Jedes Objekt wird verschlüsselt gespeichert. Das muss auch ich machen, da ich ja das Schloss abschliessen muss.
 
