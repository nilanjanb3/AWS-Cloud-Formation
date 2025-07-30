# 🔄 Stack Update Behaviors, Rollbacks & Update Policies

---

## 🔁 **Stack Update Behaviors**

* **How Updates Work:**
  When you update a stack (new template or parameters), CloudFormation computes the difference and updates only what’s changed.
* **Possible Changes:**

  * *In-place updates* (e.g., modifying instance type)
  * *Resource replacement* (e.g., changing an immutable property like VPC ID—old resource deleted, new one created)
* **Visible via Change Sets:**
  Preview all update actions before executing.

---

## 🧯 **Rollbacks During Updates**

* **Automatic Rollback:**
  If an update fails (e.g., resource error, permission issue), CloudFormation rolls back the stack to its previous stable state.
* **Partial Updates:**

  * Some resources may be updated before failure; rollback will attempt to reverse those changes.
  * Resources with `DeletionPolicy: Retain` or external dependencies might remain, so always check after a rollback.
* **Notifications:**
  Get alerts via SNS/email if rollback occurs (configure in stack options).

---

## ⚙️ **Update Policies**

* **What:**
  Special policies that control how updates are applied to certain resources, mainly Auto Scaling groups.
* **Common Policy: `UpdatePolicy`**

  * Controls rolling updates, batch sizes, pause times, and health checks for Auto Scaling groups.
* **Example:**

  ```yaml
  Resources:
    MyASG:
      Type: AWS::AutoScaling::AutoScalingGroup
      UpdatePolicy:
        AutoScalingRollingUpdate:
          MinInstancesInService: 1
          MaxBatchSize: 2
          PauseTime: PT5M
  ```
* **Best Practice:**
  Use rolling updates for zero-downtime deployments of EC2 fleets or ECS services.

---

## 🚦 **Best Practices**

* **Always review update actions** with a change set before applying to production.
* **Tag all resources and enable notifications** for monitoring updates and rollbacks.
* **Test updates in lower environments** before promoting changes to production.
* **Document manual steps required after rollbacks** (for retained resources or failed cleanup).

---

## ✨ **Pro Tip**

Use update policies and change sets to avoid downtime and surprises.
Always monitor your stack events during updates—quick action can prevent bigger issues! 🚨
