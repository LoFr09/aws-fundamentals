# KMS vs. CloudHSM – Korrektur

## Mein Fehler vorher
Ich hatte CloudHSM mit „mehr Kontrolle" begründet. Das ist zu ungenau. „Mehr Kontrolle" ist kein echter Grund.

## Wann reicht KMS?
Fast immer. KMS macht die Schlüssel-Arbeit für mich. Günstig, einfach, mit Audit (CloudTrail) und Rotation.

Neu: Seit Februar 2025 ist KMS selbst auf **FIPS 140-3 Level 3** (vorher nur Level 2).

→ Die alte Regel „für Level 3 brauchst du CloudHSM" gilt nicht mehr. KMS reicht jetzt auch für Level 3.

[*Beleg: AWS Security Blog, 20.02.2025.*](https://aws.amazon.com/de/blogs/security/aws-kms-now-fips-140-2-level-3-what-does-this-mean-for-you/)

## Wann muss es CloudHSM sein?
Nur wenn eine Vorschrift ein **eigenes Gerät nur für dich** verlangt. Also ein HSM, das niemand sonst mitbenutzt, und bei dem nur du die Schlüssel hast.

Kurz: eigenes Gerät + nur du hast die Schlüssel. Sonst nicht.

## Die drei Wege
- **SSE-S3**: S3 macht die Schlüssel selbst, du musst nichts tun, hast aber keine Kontrolle.
- **SSE-KMS**: Schlüssel liegen in KMS, du steuerst sie (Policy, Rotation, Audit).
- **CloudHSM**: eigenes Gerät nur für dich, nur wenn eine Vorschrift das verlangt.