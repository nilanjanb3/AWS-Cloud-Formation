# 🛡️ Stack Policies, Service Control Policies (SCP), & Resource-Level Controls

---

## 📜 **Stack Policies**

* **What:**
  JSON policies that control what resources in a stack can be updated during stack updates.
* **Why:**
  Prevent accidental or unauthorized changes to critical resources (e.g., don’t allow S3 buckets or IAM roles to be replaced).
* **How:**
  Attach `StackPolicyBody` (inline) or `StackPolicyURL` (S3) to a stack.
* **Example:**

  ```yaml
  StackPolicyBody:
    Statement:
      - Effect: Deny
        Action: 'Update:*'
        Principal: '*'
        Resource: 'LogicalResourceId/MyCriticalBucket'
      - Effect: Allow
        Action: 'Update:*'
        Principal: '*'
        Resource: '*'
  ```
* **Real-world use:**
  Block deletion/replacement of production databases, VPCs, or core IAM roles during automated deployments.

---

## 🏛️ **Service Control Policies (SCP)**

* **What:**
  Organization-level guardrails (via AWS Organizations) to restrict what actions AWS accounts can perform—applies to all users and roles in attached accounts.
* **Why:**
  Enforce security/compliance by blocking disallowed actions (e.g., can’t create resources in certain regions, can’t delete CloudTrail).
* **How:**
  Define SCP in AWS Organizations, attach to organizational units (OUs) or accounts.
* **Example:**

  ```json
  {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Effect": "Deny",
        "Action": "ec2:TerminateInstances",
        "Resource": "*"
      }
    ]
  }
  ```
* **Real-world use:**
  Prevent data loss by blocking instance termination or S3 deletion organization-wide.

---

## 🏷️ **Resource-Level Controls**

* **What:**
  Fine-grained IAM policies or conditions applied to individual AWS resources (like S3, DynamoDB, SQS).
* **Why:**
  Control who/what can access or modify a specific resource.
* **How:**
  Attach resource policies or use IAM policies with ARNs and conditions.
* **Example (S3 Bucket Policy):**

  ```json
  {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Effect": "Allow",
        "Principal": { "AWS": "arn:aws:iam::111122223333:role/ReadOnlyRole" },
        "Action": "s3:GetObject",
        "Resource": "arn:aws:s3:::my-bucket/*"
      }
    ]
  }
  ```
* **Real-world use:**
  Grant only specific roles/applications access to a resource (e.g., Lambda can read but not write to S3).

---

## 🚦 **Best Practices**

* **Stack Policies:**
  Protect irreplaceable resources in all critical stacks—always review before updates.
* **SCPs:**
  Use as organizational guardrails; apply “deny” rules carefully.
* **Resource Policies:**
  Always scope permissions by principal, action, and resource. Use conditions for extra security (e.g., IP, VPC, tags).

---

## ✨ **Pro Tip**

Use all three layers—stack, account/org, and resource—for full-spectrum control and compliance in large AWS environments! 🏢
