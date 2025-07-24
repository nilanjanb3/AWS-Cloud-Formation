### 🧪 Lab: Set Up Stack Policies to Protect Critical Resources in CloudFormation

---

## 📌 Objective

Apply a **CloudFormation stack policy** to prevent accidental updates or deletions of critical resources (like S3 buckets, IAM roles, etc.), while still allowing safe updates to other resources.

---

## 📁 Template: `s3-protected-policy-stack.yaml`

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: S3 bucket and Lambda with stack policy protection

Resources:
  CriticalBucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: !Sub "protected-bucket-${AWS::AccountId}-${AWS::Region}"
      Tags:
        - Key: Name
          Value: critical

  UtilityLambda:
    Type: AWS::Lambda::Function
    Properties:
      Handler: index.lambda_handler
      Runtime: python3.12
      Role: arn:aws:iam::123456789012:role/lambda-basic-execution
      Code:
        ZipFile: |
          def lambda_handler(event, context):
              return "OK"
```

---

## 📜 Stack Policy Example: `stack-policy.json`

```json
[
  {
    "Effect": "Deny",
    "Action": "Update:*",
    "Principal": "*",
    "Resource": "LogicalResourceId/CriticalBucket"
  },
  {
    "Effect": "Allow",
    "Action": "Update:*",
    "Principal": "*",
    "Resource": "*"
  }
]
```

* **Prevents any update or delete to `CriticalBucket` via CloudFormation, but allows updates to other resources.**

---

## 🚦 Deploy

### 1. Create the stack with the stack policy:

```bash
aws cloudformation create-stack \
  --stack-name protected-stack \
  --template-body file://s3-protected-policy-stack.yaml \
  --stack-policy-body file://stack-policy.json \
  --capabilities CAPABILITY_NAMED_IAM
```

### 2. (Optional) Update the stack policy later:

```bash
aws cloudformation set-stack-policy \
  --stack-name protected-stack \
  --stack-policy-body file://stack-policy.json
```

---

## 🧩 How It Works

* **Stack policies** are attached to the stack (not individual resources).
* All attempts to update/delete the protected resource via CloudFormation are denied.
* Allows safer updates for non-critical resources.

---

## ✅ Key Concepts Practiced

| Concept           | Used In This Lab                      |
| ----------------- | ------------------------------------- |
| Stack Policy      | JSON policy file for stack protection |
| LogicalResourceId | Resource-level protection in stack    |
| Update/Deletion   | Prevent changes to critical resources |

---

## 📘 Best Practices

* Always protect **critical data resources** (S3, IAM, DB) with stack policies.
* Review stack policies during stack updates and before destructive changes.
* Use **logical resource IDs** (not physical IDs) in stack policies.

Ready for the next lab.
