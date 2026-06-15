# IAM Vertiefung 

## Policies


### Readonly
https://docs.aws.amazon.com/aws-managed-policy/latest/reference/ReadOnlyAccess.html
Die Police ist fast 3000 Zeilen lang, deshalb würde das dieses Dokument sprengen, aber ich habe sie unter der oberen AWS Seite gefunden.

### S3-User
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
                "arn:aws:s3:::bbucket-for-s3-user/*"
            ]
        }
    ]
}

### EC2-Operator
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowStartStop",
            "Effect": "Allow",
            "Action": [
                "ec2:StartInstances",
                "ec2:StopInstances"
            ],
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