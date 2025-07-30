# 🔄 Change Sets: Previewing & Approving Stack Updates

---

## 👀 **What Are Change Sets?**

* Change Sets let you **preview** what changes will happen before CloudFormation actually updates your stack.
* Shows resources to be **added, modified, or deleted**—reducing surprises and risk.

---

## ⚙️ **How to Use Change Sets**

1. **Create a Change Set**

   * Propose a change by uploading a modified template or changing parameters.
   * Use Console, CLI, or API.

     ```sh
     aws cloudformation create-change-set \
       --stack-name my-stack \
       --template-body file://updated.yaml \
       --change-set-name my-update
     ```

2. **Review the Change Set**

   * See the full list of proposed changes—additions, replacements, deletions, property updates.
   * Validate changes with your team (or as part of a CI/CD pipeline).

3. **Approve (Execute) or Delete**

   * If changes look good, execute the change set:

     ```sh
     aws cloudformation execute-change-set --change-set-name my-update --stack-name my-stack
     ```
   * Or delete/cancel if not approved.

---

## 🚦 **Best Practices**

* **Always use change sets for production stacks**—avoid “click and pray” deployments.
* Review all deletions and replacements carefully—they may cause downtime or data loss.
* Integrate change set creation & review into your CI/CD workflow, with approvals required before execution.
* Document who approved the change for audit and compliance.

---

## 💡 **Real-World Example**

* Enterprise teams require a senior engineer or manager to review and approve all stack changes using change sets before deployment in production.

---

## ✨ **Pro Tip**

Change sets are your “undo/redo preview” for infrastructure—**use them to catch mistakes before they cost you!** 🧐
