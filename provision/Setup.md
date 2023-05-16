#!/bin/bash
#
# From : https://marco-urrea.medium.com/hashicorp-vault-aws-secrets-engine-3297bf3ffbd4
#
vault secrets enable -path=aws aws

vault write aws/config/root \
    access_key=<Your AWS Access key ID> \
    secret_key=<Your AWS Secret access key> \
    region=us-east-1

vault write aws/roles/my-role \
        credential_type=iam_user \
        policy_document=-<<EOF
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "Stmt1426528957000",
      "Effect": "Allow",
      "Action": [
        "ec2:*"
      ],
      "Resource": [
        "*"
      ]
    }
  ]
}
EOF

vault read aws/creds/my-role

# Sample of the return data :
# Key                Value
# ---                -----
#lease_id           aws/creds/my-role/XHHsCOS135tbkD0gMz9A8ge
#lease_duration     768h
#lease_renewable    true
#access_key         AKIATOT...
#secret_key         RqLzsRWVDcNcqMH...
#security_token     <nil>


# STS federated token generation

vault write aws/roles/ec2_admin \
    credential_type=federation_token \
    policy_document=@policy.json

#Where policy.json is 
#{
#    "Version": "2012-10-17",
#    "Statement": {
#        "Effect": "Allow",
#        "Action": [
#            "ec2:*",
#            "sts:GetFederationToken"
#        ],
#        "Resource": "*"
#    }
#}


vault write aws/sts/ec2_admin ttl=60m

vault lease revoke aws/sts/ec2_admin/AAEHbv7esgcAfjBysVCegseg5

