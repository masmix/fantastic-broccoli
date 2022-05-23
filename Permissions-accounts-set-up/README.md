# Purpose 

> Below steps are optional if your IAM user have not enough permissions cloudformation, s3 and kms services to create all required resources from
> Here we will configure permissions for tools account for IAM role only for this particular permission.

# Prereqisities

- AWS CLI installed
- URL for Organisation SSO Dashbard is known. 

Example URL
```console
https://d-93677b80d9.awsapps.com/start#/
```

# 1. Configuration steps
- get programatic acess keys from Dev-source account 
![](../../images/AWS-SSO-Dashboard.png)

![](../../images/get-aws-credentials.png)

- copy data to clipboard from point 2 

```console
[developer1]
aws_access_key_id=xxxx
aws_secret_access_key=xxxx
aws_session_token=xxxx
```

- paste into .aws/credentials

```console
[developer1]
aws_access_key_id=xxx
aws_secret_access_key=xxx
aws_session_token=xxx
```

- paste into ~.aws/config
```console
[profile developer]
region = eu-west-1
output = json
```

- verify identity
```sh
aws sts get-caller-identity --profile developer1
```
- create IAM admin user
```sh
aws iam create-user --user-name pulumi --profile developer1
```
- attach policy to user
```sh
aws iam create-policy --policy-name aws-refarch-cross-account-pipeline-sts-and-cloudformation-policy --policy-document file://Permissions-accounts-set-up/tools-admin-user-policy.json --profile developer1
```
- attach policy to user
```sh
aws iam attach-user-policy --user-name pulumi --policy-arn "arn:aws:iam::{aws_account_id}:policy/aws-refarch-cross-account-pipeline-sts-and-cloudformation-policy" --profile developer1
```
- create IAM role
```sh
aws iam create-role --role-name aws-refarch-cross-account-pipeline-service-role --assume-role-policy-document file://Permissions-accounts-set-up/trust-relationship-policy.json --profile developer1
```
- attach policy to role
```sh
aws iam attach-role-policy --role-name aws-refarch-cross-account-pipeline-service-role --policy-arn "arn:aws:iam::{aws_account_id}:policy/aws-refarch-cross-account-pipeline-sts-and-cloudformation-policy" --profile developer1
```
- verify 
```sh
aws iam list-attached-role-policies --role-name aws-refarch-cross-account-pipeline-service-role --profile developer1
```
- create user credentials
```sh
aws iam create-access-key --user-name pulumi --profile developer1
```
- configure default profile 
```sh
aws configure 
AWS Access Key ID [None]: ExampleAccessKeyID1
AWS Secret Access Key [None]: ExampleSecretKey1
Default region name [None]: eu-west-1
Default output format [None]: json
```
- verify
```sh
aws sts get-caller-identity
```
Expected output:
```json
{
    "UserId": "AIDA2UAQ2YM2ZZ4NNGFPX",
    "Account": "730179748661",
    "Arn": "arn:aws:iam::730179748661:user/pulumi"
}
```

# 2. Role assume configuration (optional)

- asume role
```sh
aws sts assume-role --role-arn "arn:aws:iam::{YOUR_TOOLS_ACCOUNT_ID}:role/aws-refarch-cross-account-pipeline-service-role-2" --role-session-name AWSCLI-Session --profile tools_admin
```

Example ouput:


- Paste the following text in your AWS credentials file (typically found at ~/.aws/credentials). 
```sh
[tools_admin_deployer]
aws_access_key_id = accessKeyIdValue
aws_secret_access_key = secretAccessKeyValue
aws_session_token = sessionTokenValue

```

- verify 
```sh
aws sts get-caller-identity --profile tools_admin_deployer
```
Example output
```json
{
    "UserId": "AROAVOS2MKF2GKH3OSZBB:AWSCLI-Session",
    "Account": "{YOUR_TOOLS_ACCOUNT_ID}",
    "Arn": "arn:aws:sts::{YOUR_TOOLS_ACCOUNT_ID}:assumed-role/aws-refarch-cross-account-pipeline-service-role-2/AWSCLI-Session"
}
```
# Finish

From now default aws profile for cli should works. 

You can continue with next [steps](../README.md)

# References

[Video abou STS](https://www.youtube.com/watch?v=-uogKFE1r60)

