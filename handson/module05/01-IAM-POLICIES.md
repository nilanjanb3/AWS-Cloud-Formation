### 🧪 Lab: Implement IAM with Scoped-Down Policies Using CloudFormation

---

## 📌 Objective

Provision an **IAM user**, **role**, and **group** with **least-privilege (scoped-down)** policies for a real-world scenario—granting access to just one S3 bucket and read-only DynamoDB, using CloudFormation best practices.

---

## 🛠️ Problem Scenario

You want to allow:

* **Developers** to list and put objects in a specific S3 bucket (not full S3 access)
* **App automation** (via IAM role) to read-only access to DynamoDB (not write/delete)
* **IAM group** for easy team permissions management

---

## 📁 Template: `iam-scoped-down.yaml`

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: IAM least-privilege user, role, and group with scoped-down policies

Parameters:
  S3BucketName:
    Type: String
    Default: my-app-bucket

Resources:

  # IAM Group for developers
  DevGroup:
    Type: AWS::IAM::Group
    Properties:
      GroupName: developers

  # Policy: S3 scoped access
  S3ScopedPolicy:
    Type: AWS::IAM::Policy
    Properties:
      PolicyName: s3-scoped-access
      Groups: [ !Ref DevGroup ]
      PolicyDocument:
        Version: "2012-10-17"
        Statement:
          - Effect: Allow
            Action:
              - s3:ListBucket
            Resource: !Sub arn:aws:s3:::${S3BucketName}
          - Effect: Allow
            Action:
              - s3:GetObject
              - s3:PutObject
            Resource: !Sub arn:aws:s3:::${S3BucketName}/*

  # IAM User for a developer
  DevUser:
    Type: AWS::IAM::User
    Properties:
      Groups: [ !Ref DevGroup ]
      UserName: dev-user

  # IAM Role for app automation (read-only DynamoDB)
  AppAutomationRole:
    Type: AWS::IAM::Role
    Properties:
      RoleName: app-automation-role
      AssumeRolePolicyDocument:
        Version: "2012-10-17"
        Statement:
          - Effect: Allow
            Principal:
              Service: ec2.amazonaws.com
            Action: sts:AssumeRole
      ManagedPolicyArns: []
      Policies:
        - PolicyName: dynamodb-readonly
          PolicyDocument:
            Version: "2012-10-17"
            Statement:
              - Effect: Allow
                Action:
                  - dynamodb:Scan
                  - dynamodb:Query
                  - dynamodb:GetItem
                  - dynamodb:BatchGetItem
                  - dynamodb:DescribeTable
                Resource: "*"

Outputs:
  DevUserName:
    Value: !Ref DevUser
  AppAutomationRoleName:
    Value: !Ref AppAutomationRole
  DevGroupName:
    Value: !Ref DevGroup
```

---

## 🚀 Deploy

```bash
aws cloudformation deploy \
  --template-file iam-scoped-down.yaml \
  --stack-name iam-least-privilege \
  --capabilities CAPABILITY_NAMED_IAM \
  --parameter-overrides S3BucketName=your-bucket-name
```

---

## ✅ Key Concepts Practiced

| Concept                 | Example in Lab                       |
| ----------------------- | ------------------------------------ |
| Scoped policies         | Restrict S3/DynamoDB permissions     |
| IAM user/group/role     | Dev user, group, and automation role |
| Least privilege         | Only minimal actions allowed         |
| Parameterization        | Bucket name via parameter            |
| Inline and group policy | Used for flexible management         |

---

## 📘 Best Practices

* Always follow the **principle of least privilege**
* Use **groups** to manage user permissions at scale
* **Parameterize** resource names for reusability
* **Explicitly scope resources** in policy (avoid `"*"` except for DynamoDB in this lab demo)
* Use separate roles for automation (do not reuse user credentials)

Ready for the next lab.
