# Bucket Policy erklärt:

Ich mache es so, ich gehe Zeile für Zeile durch und schriebe immer einen Kommentar rechts daneben.


```json

{
    "Version": "2012-10-17",                                              // Regelwerk-Version
    "Statement": [                                                       // Liste der Regeln
        {                                                                // Regel 1
            "Sid": "NurS3User",                                          // Name
            "Effect": "Deny",                                            // Verbieten
            "Principal": "*",                                            // Für alle
            "Action": "s3:*",                                            // Alles in S3
            "Resource": [                                                // Gilt für:
                "arn:aws:s3:::aws-fundamentals-02",                      // Bucket
                "arn:aws:s3:::aws-fundamentals-02/*"                     // Inhalt
            ],
            "Condition": {                                               // Bedingung
                "StringNotLike": {                                       // Wenn nicht
                    "aws:PrincipalArn": "arn:aws:iam::236353235096:user/s3-user"  // dieser User
                }
            }
        },
        {                                                                // Regel 2
            "Sid": "NurHTTPS",                                           // Name
            "Effect": "Deny",                                            // Verbieten
            "Principal": "*",                                            // Für alle
            "Action": "s3:*",                                            // Alles in S3
            "Resource": [                                                // Gilt für:
                "arn:aws:s3:::aws-fundamentals-02",                      // Bucket
                "arn:aws:s3:::aws-fundamentals-02/*"                     // Inhalt
            ],
            "Condition": {                                               // Bedingung
                "Bool": {                                                // Wenn
                    "aws:SecureTransport": "false"                       // ohne HTTPS
                }
            }
        },
        {                                                                // Regel 3
            "Sid": "NurVerschlüsselteUploads",                           // Name
            "Effect": "Deny",                                            // Verbieten
            "Principal": "*",                                            // Für alle
            "Action": "s3:PutObject",                                    // Hochladen
            "Resource": "arn:aws:s3:::aws-fundamentals-02/*",            // Inhalt
            "Condition": {                                               // Bedingung
                "Null": {                                                // Wenn fehlt
                    "s3:x-amz-server-side-encryption": "true"            // Verschlüsselung fehlt
                }
            }
        }
    ]
}

```

*Bei der Regel 3 hatte ich am meisten mühe, ich musste nachschauen was das mit der encryption war, da war ich mir nicht sicher.*