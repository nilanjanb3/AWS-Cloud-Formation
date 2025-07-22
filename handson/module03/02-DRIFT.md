### 🧪 Lab: Simulate and Detect CloudFormation Configuration Drift

---

## 📌 Objective

Learn how to **simulate configuration drift** (manual change outside CloudFormation) and then **detect** it using CloudFormation’s drift detection features.

---

## 🏗️ 1. Deploy a Simple Stack

Example: `drift-lab-stack.yaml`

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: Simple stack for drift detection lab

Resources:
  DriftLabBucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: !Sub drift-lab-bucket-${AWS::AccountId}-${AWS::Region}
      Tags:
        - Key: Owner
          Value: CloudFormation
```

Deploy the stack:

```bash
aws cloudformation deploy \
  --template-file drift-lab-stack.yaml \
  --stack-name drift-lab-stack
```

---

## 🛠️ 2. Simulate Drift

**Manually change the S3 bucket outside CloudFormation:**

* Go to AWS Console → S3 → Find the bucket created by the stack.
* Add a new tag, e.g., `Environment=Test` (or delete the `Owner` tag).

---

## 🕵️ 3. Detect Drift

**Run drift detection:**

```bash
aws cloudformation detect-stack-drift --stack-name drift-lab-stack
```

Check status:

```bash
aws cloudformation describe-stack-drift-detection-status \
  --stack-drift-detection-id <output-from-previous-command>
```

After a minute, check drift status/results:

```bash
aws cloudformation describe-stack-resource-drifts \
  --stack-name drift-lab-stack
```

Or view drift details in the **CloudFormation Console** (under the "Drift status" tab for your stack).

---

## ✅ Key Concepts Practiced

| Concept          | Command/Location                                    |
| ---------------- | --------------------------------------------------- |
| Drift simulation | Manual resource modification outside CloudFormation |
| Drift detection  | `detect-stack-drift` & drift status CLI/console     |
| Remediation      | Update stack or use `cloudformation drift status`   |

---

## 📘 Best Practices

* Detect drift **regularly** in automation for production stacks.
* Use drift detection before making stack updates.
* Investigate and remediate drift to maintain IaC consistency.
* Use tags or stack policies to prevent accidental changes.

Ready for the next lab.
