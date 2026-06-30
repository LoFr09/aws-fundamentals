# Explain-Back
Schriftlich, in eigenen Worten, kein Copy-Paste, keine KI-Prosa. Die Fragen beziehen sich auf deine Artefakte — allgemeine Antworten zählen nicht.

1. Zeig an deiner `ec2-operator`-Policy die konkrete Stelle, die das Terminieren verhindert. Greift bei dir ein **impliziter** oder ein **expliziter** Deny? Wenn du eine einzige Änderung machen wolltest, sodass Terminate erlaubt wäre — welche genau, und reicht eine?


        {
            "Sid": "DenyTermination",
            "Effect": "Deny",
            "Action": "ec2:TerminateInstances",
            "Resource": "*"
        }

Hier die Stelle die das verhindert, wenn man nun das Deny durch ein Allow ersetzt wird es erlaubt (Man könnte den Namen noch anpassen aber nicht OB)

        {
            "Sid": "DenyTermination",
            "Effect": "Allow",
            "Action": "ec2:TerminateInstances",
            "Resource": "*"
        }

Wir haben hier einen Expliziter Deny das sehen wir dran das wir den Deny geschrieben haben. Wenn wir einfach nichts geschrieben hätten, hätte es auch einen Deny gegeben da AWS alles was nicht Explizit erlaubt ist ablehnt, das nennt man einen Impliziter Deny

https://docs.aws.amazon.com/de_de/IAM/latest/UserGuide/reference_policies_evaluation-logic_AccessPolicyLanguage_Interplay.html

---

2. Deine alte `readonly-user`-Lösung (`ReadOnlyAccess`) und deine neue Hand-Policy verbieten beide das Schreiben. Warum ist die Hand-Policy trotzdem die bessere Lösung? Nenne eine konkrete Berechtigung, die `ReadOnlyAccess` mitbringt und deine Hand-Policy bewusst nicht.

 - Die `ReadOnlyAccess` erlaubt read only auf alle alle Dienste, das wollen wir nich da das nicht Least Privileg ist. Deshalbnhabe ich bei der Hand-Policy nur EC2, S3 und CloudWatch Read erlaubt. 

---

3. Erkläre die `Null`-Condition in deiner Bucket-Policy so, als würdest du sie einem anderen Lernenden beibringen: Welcher Upload wird geblockt, welcher nicht, und warum braucht es dafür `Null` und nicht einfach einen Vergleich auf den Header-Wert?

    - Also die Null-Condition prüft, ob der Verschlüsselungs Header da ist(Der Verschlüsselungs Header ist das was ich nachschauen musst in Regel 3,  das x-amz-server-side-encryption, das ist zum dasgen verschlüssele dies Objekt) Wenn er fehlt, wir der Upload Blockier. Null braucht es, weil ein Wert-Vergleich nicht funktioniert, wenn kein Header kommt.

---

4. Ein Kunde sagt: „Ich will volle Kontrolle über meine Schlüssel, nimm CloudHSM." Wann ist das die richtige Antwort und wann übertrieben? Mach den Unterschied an einem konkreten Kriterium fest — nicht an „mehr Kontrolle".

     - Wenn der Kunde wirklich die alleinige Kontrolle haben muss und er umbedigt Level 3 braucht den ist CloudHSM richtig (bin nicht sicher da es ja seit 2025 auch mit KMS geht) und er nur für ein einziges gerät ist dan ist CloudHSM richtig. Wenn er einfach der Schlüssel Steueren möchte und er aber in der KMS bleiber soll reicht SSE-KMS voll aus.


*Ich habe das Explainback so ausgefüllt wie ich es verstanden habe, währe froh um eine Rückmeldung wenn etwas gar nicht stimmt so oder ich es falsch verstanden habe*