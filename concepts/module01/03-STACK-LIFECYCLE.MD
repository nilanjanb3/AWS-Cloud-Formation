# ⏳ Stack Lifecycle & Dependencies in CloudFormation 🔗

---

## 🌱 **Stack Lifecycle: Key Phases**

1. **Create**

   * Stack is launched from a template.
   * All defined resources are created in order, handling dependencies automatically.
   * Example: Creating a VPC, then subnets, then EC2 instances inside those subnets.

2. **Update**

   * Change template or parameter values; stack is updated accordingly.
   * Only changed resources (and those dependent on them) are updated or replaced.
   * Example: Updating an EC2 instance type or adding a new resource.

3. **Delete**

   * All stack resources are deleted in the correct order (reverse dependency).
   * If a resource has a `DeletionPolicy: Retain`, it is preserved.
   * Example: Deleting a stack to remove all associated AWS resources.

4. **Rollback**

   * If stack creation or update fails, AWS automatically rolls back to the last stable state, deleting any partially created resources.
   * Example: Failing to create an RDS instance due to invalid subnet causes previous resources to be cleaned up.

---

## 🔗 **Resource Dependencies**

* CloudFormation determines the order of resource creation based on dependencies.
* Some are **automatic** (e.g., an EC2 instance references a Security Group).
* Use the **`DependsOn`** attribute for explicit control when needed.

**Example:**

```yaml
Resources:
  MyBucket:
    Type: AWS::S3::Bucket
  MyBucketPolicy:
    Type: AWS::S3::BucketPolicy
    DependsOn: MyBucket  # Ensures bucket is created first
```

---

## 🚦 **Best Practices for Handling Dependencies**

* **Let CloudFormation manage dependencies** by referencing resource names (e.g., use `!Ref` and `!GetAtt`).
* Use **`DependsOn`** sparingly—only for special cases (like custom resources or side-effect timing).
* Split complex architectures into **nested stacks** or **cross-stack references** using Outputs/Imports for better management.

---

## 🏗️ **Stack Deletion Policies**

* Control what happens to resources on stack deletion using:

  * `Retain`: Keeps the resource (e.g., for S3 buckets you don’t want deleted).
  * `Snapshot`: Takes a snapshot (for RDS, EBS).
  * `Delete`: Default—removes the resource.

**Example:**

```yaml
Resources:
  MyDB:
    Type: AWS::RDS::DBInstance
    DeletionPolicy: Snapshot
```

---

## 💡 **Real-World Use Case**

* **Blue/Green Deployments:**
  Use nested stacks—one for networking, one for application. Update only the application stack for new deployments.
* **Data Retention:**
  Set `Retain` on S3 buckets and RDS databases to keep data when deleting test or staging environments.

---

## 📝 **Quick Reference**

* **Stack Lifecycle:** Create → Update → Delete → (Automatic Rollback on failure)
* **Dependencies:** Mostly managed by references; use `DependsOn` for manual control
* **DeletionPolicy:** Choose to `Retain`, `Delete`, or take a `Snapshot` as needed

---
