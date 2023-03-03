# aws-auto-remediations

## Overview
Auto-remediates public facing Security Groups

Template is written based on AWS Serverless Model Applicaiton Specification.

[AWS SAM Template Concepts](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-template-basics.html)

## Resources
- Lambda Function
- Lambda Role

## Deployment
```
aws cloudformation package --template-file remediate-public-facing-sg.yaml --s3-bucket <Deployment S3 Bucket Name > --output-template-file packaged-template.yaml 

aws cloudformation deploy --template-file packaged-template.yaml --stack-name remediate-public-facing-sg 

```
Where Deployment S3 Bucket Name is the bucket to upload the CFn template and lambda code. Can be any bucket current or new.
