## Introduction

In this project we are going to be implementing quickstart AWS - Python solution using Pulumi. 
Based on [Get started guide]https://www.pulumi.com/docs/get-started/aws/
[Pulumi codebase](https://github.com/pulumi/pulumi)


# What we do?

- Setting up and configuring Pulumi to access your AWS account.
- Creating a new Pulumi project.
- Provisioning a new AWS S3 bucket.
- Adding an index.html file to your bucket and serving it as a static website.
- Cleaning up your deployment by destroying the resources you've provisioned.

#### Pre-requisites 
1. Install the [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) 
2. [Create specific user with restricted permissons (optional)](Permissions-accounts-set-up/README.md) 
2. Install Pulumi https://www.pulumi.com/docs/get-started/aws/begin/

## Steps

### Cretae new project

```sh
pip install virtualenv
cd
mkdir quickstart && cd quickstart
virtualenv venv
pulumi new aws-python
```

> If this is your first time running pulumi new or most other pulumi commands, you will be prompted to log in to the Pulumi service. 

After logging in, the CLI will proceed with walking you through creating a new project.

First, you will be asked for a project name and description. Hit ENTER to accept the default values or specify new values.

Next, you will be asked for the name of a stack. Hit ENTER to accept the default value of dev.

Finally, you will be prompted for some configuration values for the stack. For AWS projects, you will be prompted for the AWS region. You can accept the default value or choose another value like us-west-2.

After the command completes, the project and stack will be ready.

# Review new project

Let’s review some of the generated project files:

Pulumi.yaml defines the project.
Pulumi.dev.yaml contains configuration values for the stack you just initialized.
main.py is the Pulumi program that defines your stack resources.
Let’s examine __main__.py .

```python
import pulumi
from pulumi_aws import s3

# Create an AWS resource (S3 Bucket)
bucket = s3.Bucket('my-bucket')

# Export the name of the bucket
pulumi.export('bucket_name',  bucket.id)
```

This Pulumi program creates a new S3 bucket and exports the name of the bucket.


# Deploy the Stack 

```sh
pulumi up 
```


Output:
```console




# Troubleshooting

Symthomps:
Console message:

```json
 error: 1 error occurred:
        * creating urn:pulumi:dev::quickstart::aws:s3/bucket:Bucket::my-bucket: 1 error occurred:
        * error reading S3 Bucket (my-bucket-8591980): Forbidden: Forbidden
        status code: 403, request id: 7GCY34Y3VVVNV9BV, host id: c/EPJ9OTw20oNzo45jhd5tV3e447DutdFivpljl7JqTArvr7TEhEd1mBAWz3oDBC1bukbA8nUQw=
```

Solution:

- update [policy](Permissions-accounts-set-up/tools-admin-user-policy.json) with desired or missing permissions

- update policy 

```sh
aws iam create-policy-version  --policy-arn arn:aws:iam::730179748661:policy/aws-refarch-cross-account-pipeline-sts-and-cloudformation-policy  --policy-document file://Permissions-accounts-set-up/tools-admin-user-policy.json --profile developer1  --set-as-default
```