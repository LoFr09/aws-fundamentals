# Shared Responsibility Model – einfach erklaert


## 1. Wer macht was?

| Dienst | Das macht AWS | Das muss ICH machen |
|---|---|---|
| **EC2 (Server)** | Hardware, Strom, Rechenzentrum, Virtualisierung | Fast alles selbst: Betriebssystem updaten, Firewall (Security Groups), Apps, Zugriff (IAM), Daten verschlüsseln |
| **RDS (Datenbank)** | Hardware + Betriebssystem + Datenbank-Updates + Backups | Wer darf in die DB (Security Group, DB-User), Verschlüsselung anschalten, Daten schützen |
| **S3 (Speicher)** | Hardware + alles drumherum + stellt die Verschlüsselung bereit | Bucket-Policy setzen, Public Access blockieren, Verschlüsselung beim Upload anschalten, Zugriff (IAM), Daten |

*Ich habe hier einfach die Sachen aufgeschrieben die ich heraus gefunden habe, da ich nicht ganz sicher bin was gemeint ist mit genauen einstellungen. Hoffe das ist Richtig so.*

https://aws.amazon.com/de/blogs/industries/applying-the-aws-shared-responsibility-model-to-your-gxp-solution/

## 2. Warum ändert sich, wer was macht?

Ganz einfach: Je mehr AWS für mich verwaltet, desto weniger bleibt für mich.

- **EC2:** AWS gibt mir nur den nackten Server. Den Rest mache ich.
- **RDS:** AWS übernimmt zusätzlich das Betriebssystem und die Datenbankupdates.
- **S3:** AWS macht fast alles. Ich kümmere mich nur noch um Zugriff und Daten.

**Merksatz:** Egal welches Modell, für meine eigenen Daten und wer darauf zugreift, bin immer ich/der Admin verantwortlich.

## 3. "OF the Cloud" vs. "IN the Cloud"

| | Wer? | Einfach gesagt |
|---|---|---|
| **OF the Cloud** | AWS | Sicherheit *der* Cloud: die Technik im Hintergrund (Hardware, Server, Netzwerk) |
| **IN the Cloud** | ICH | Sicherheit *in* der Cloud: meine Daten, meine Einstellungen, wer Zugriff hat |

Beispiel aus der Übung: AWS stellt die Verschluesselung bereit. Ich habe sie beim Upload angeschaltet und die Bucket-Policy gesetzt.

## 4. Fazit

AWS gibt mir die Werkzeuge, aber anschalten muss ich sie selbst. In der Uebung sieht man das gut: Der Upload ging nur mit Verschluesselung durch, und fremde User wurden geblockt – beides, weil ICH es richtig eingestellt habe. Geht etwas schief, ist es meistens ein Fehler bei meiner Einstellung, nicht bei AWS.