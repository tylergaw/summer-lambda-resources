# Summer AWS Lambda Resources

Cloudformation template for building the necessary policy and role to enable Summer to invoke a lambda in your AWS account to create a Connector.

The template is meant for use with the aws cli. You can also manually create the resources in the AWS console following the examples below.

### How to run this with the aws cli

**Note**: To use the cli you will need to have local access keys set up for an account with all required permissions.

Replace `org_XXXXXXXXX` in the command below with your Summer organization ID and run it to create the stack and resources.

```
aws cloudformation create-stack --stack-name SummerLambdaStack --template-body file://template.yaml --parameters ParameterKey=ExternalId,ParameterValue=org_XXXXXXXXX --capabilities CAPABILITY_NAMED_IAM
```

You will need the Role ARN to create a Connector in Summer. You can find that in the AWS console, or using the following commands:

```
aws cloudformation wait stack-create-complete --stack-name SummerLambdaStack
```

Followed by

```
aws cloudformation describe-stacks --stack-name SummerLambdaStack --query 'Stacks[0].Outputs[?OutputKey==`RoleArn`].OutputValue' --output text
```

It should look something like `arn:aws:iam::XXXXXXXX:role/SummerLambdaRole`

### Resources the template creates

This creates two new resources in your AWS account.

#### An IAM policy

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "InvokePermission",
      "Effect": "Allow",
      "Action": "lambda:InvokeFunction",
      "Resource": "*"
    }
  ]
}
```

#### An IAM role

```json
{
  "RoleName": "SummerLambdaRole",
  "AssumeRolePolicyDocument": {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Effect": "Allow",
        "Principal": {
          "AWS": "arn:aws:iam::415991094359:root"
        },
        "Action": "sts:AssumeRole",
        "Condition": {
          "StringEquals": {
            "sts:ExternalId": "org_XXXXXXXXXX"
          }
        }
      }
    ]
  }
}
```
