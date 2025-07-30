# 🗑️ Deletion Policies & Resource Protection in CloudFormation

---

## 🏷️ **Deletion Policies**

* **What:**
  Deletion policies control what happens to specific resources when a stack is deleted or updated.
* **Supported Values:**

  * `Delete` (default): Resource is removed when the stack is deleted.
  * `Retain`: Resource is preserved—even if the stack is deleted.
  * `Snapshot`: For certain resources (e.g., RDS, EBS), take a snapshot before deletion.
* **How to Use:**

  ```yaml
  Resources:
    MyDB:
      Type: AWS::RDS::DBInstance
      DeletionPolicy: Snapshot
    MyS3Bucket:
      Type: AWS::S3::Bucket
      DeletionPolicy: Retain
  ```
* **Real-World Example:**
  Use `Retain` for S3 buckets with critical data, or `Snapshot` for RDS databases to prevent data loss on accidental stack deletion.

---

## 🛡️ **Resource Protection**

### 1. **Stack Policies**

* Prevent accidental updates or deletions of sensitive resources within a stack.
* Attach a stack policy that denies updates/replacements to critical resources.

### 2. **Termination Protection**

* Prevents the entire stack from being deleted (even by accident).
* Enable via Console, CLI, or API:

  ```sh
  aws cloudformation update-termination-protection --stack-name my-stack --enable-termination-protection
  ```

### 3. **Resource-Level Controls**

* Use IAM policies or service-specific resource policies to limit who can delete or modify resources.

---

## 🚦 **Best Practices**

* Apply `Retain` or `Snapshot` to all critical or irreplaceable data resources.
* Always enable stack termination protection for production stacks.
* Use stack policies to safeguard core infrastructure components.
* Regularly review and update resource-level permissions and controls.

---

## ✨ **Pro Tip**

**Plan for accidents**—use deletion policies and resource protection features to prevent data loss and downtime! 🛑
