# 🛡️ Automated Rollback & Drift Detection in CloudFormation

---

## 🔁 **Automated Rollback**

* **What:**
  If stack creation or update fails, CloudFormation automatically rolls back to the last stable state—no partial resources left.

* **How it works:**

  * On failure, resources created/modified during the operation are deleted or reverted.
  * You get notifications (SNS, email) if configured.

* **Key Settings:**

  * `DisableRollback: false` (default): Ensures rollback happens automatically.
  * Can customize with rollback triggers (e.g., CloudWatch Alarms).

* **Example:**

  1. Deploy stack to create EC2 and RDS.
  2. If RDS fails to create, EC2 is also deleted.
  3. You’re left with no partial infrastructure—safe and clean.

* **Best Practice:**

  * Always leave rollback **enabled** in production.
  * Use `RollbackConfiguration` to trigger rollback on external alerts (like metric spikes or failed health checks).

---

## 🛰️ **Drift Detection**

* **What:**
  Drift detection checks whether the actual state of stack resources matches what’s defined in your template.

* **Why:**
  Detects manual changes (“drift”) outside CloudFormation—helps keep infrastructure consistent and predictable.

* **How to use:**

  * Run drift detection from AWS Console, CLI, or API:

    ```sh
    aws cloudformation detect-stack-drift --stack-name my-stack
    ```
  * Review drift status and details.

* **Types of drift:**

  * Resource property changes (e.g., security group rules modified manually)
  * Resource deleted/added outside CloudFormation

* **Example Workflow:**

  1. Team member manually opens port in a Security Group.
  2. Run drift detection—CloudFormation flags the SG as “drifted.”
  3. Review differences, and re-align as needed.

---

## 🚦 **Best Practices**

* Enable notifications (SNS, email) for rollback and drift detection events.
* Run drift detection regularly (especially before updates or audits).
* Fix drift by updating via CloudFormation templates—never manual changes.

---

## 📝 **Quick Reference Table**

| Feature            | Purpose                  | How to Use                         | Real-world Benefit              |
| ------------------ | ------------------------ | ---------------------------------- | ------------------------------- |
| Automated Rollback | Safe, clean deployments  | Default setting, rollback triggers | No partial infra, easy recovery |
| Drift Detection    | Consistency & compliance | Console/CLI/API                    | Detects manual errors           |

---

## ✨ **Pro Tip**

Automated rollback protects you from failed changes, and drift detection keeps your infrastructure “as code”—never let manual edits break your pipeline! 🚨
