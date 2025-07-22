# 🚦 CI/CD Patterns for CloudFormation: Blue/Green & Canary

---

## 🔵🟢 **Blue/Green Deployments**

* **What:**
  Run two identical environments—“Blue” (current) and “Green” (new). Cut traffic to “Green” only after validation.
* **How with CloudFormation:**

  * Maintain separate stacks for Blue and Green environments (e.g., `app-blue` and `app-green`).
  * Deploy the new version to the inactive stack (Green).
  * Switch the DNS (Route53), Load Balancer, or update an environment variable to direct users to Green after testing.
  * Rollback is fast—just switch back to Blue.
* **Example Flow:**

  1. `app-blue` stack is live.
  2. Deploy new app version to `app-green`.
  3. Test `app-green` using CI pipeline.
  4. Update Route53/ALB target to point to `app-green`.
  5. Optionally delete or archive `app-blue`.
* **Real-world use:**
  Financial services use Blue/Green to minimize downtime and ensure safe rollbacks during critical releases.

---

## 🐦 **Canary Deployments**

* **What:**
  Gradually shift traffic from the old version to the new version, monitoring for issues before full cutover.
* **How with CloudFormation:**

  * Use stacks or resource versions (like Lambda aliases) to incrementally deploy updates.
  * Integrate with services that support traffic shifting (e.g., Lambda, ECS, ALB).
  * Automate traffic increments via CI/CD (e.g., 10% → 50% → 100%).
* **Example:**

  * Deploy new Lambda version.
  * Use Lambda Alias with weighted routing:

    ```yaml
    MyAlias:
      Type: AWS::Lambda::Alias
      Properties:
        FunctionName: !Ref MyFunction
        FunctionVersion: !GetAtt MyFunction.Version
        RoutingConfig:
          AdditionalVersionWeights:
            - FunctionVersion: 2
              FunctionWeight: 0.1  # 10% traffic
    ```
  * Monitor and increase traffic via pipeline steps.
* **Real-world use:**
  E-commerce sites roll out new checkout logic to 5% of users, monitor for errors, then increase to 100%.

---

## ⚙️ **Typical CI/CD Flow with CloudFormation**

1. **Build**: Lint and validate templates.
2. **Deploy**:

   * Use CloudFormation Change Sets for safe previews.
   * Deploy to staging environment first.
3. **Test**: Run automated tests on the new stack/environment.
4. **Promote**:

   * Blue/Green: Switch live traffic after tests.
   * Canary: Gradually update weights and monitor.
5. **Rollback if needed**: Rapid switch or stack deletion.

---

## 🏁 **Best Practices**

* **Automate deployments** using CI/CD tools (e.g., CodePipeline, Jenkins, GitHub Actions).
* **Use Change Sets** to review changes before applying.
* **Monitor metrics and alarms** during and after deployment.
* **Tag stacks and resources** for easy tracking and rollback.
* **Document rollback steps** for every deployment strategy.

---

## ✨ **Summary Table**

| Pattern    | Purpose           | Stack Usage      | Traffic Control    | Rollback         |
| ---------- | ----------------- | ---------------- | ------------------ | ---------------- |
| Blue/Green | Zero-downtime     | Two parallel     | DNS/ALB Switch     | Immediate switch |
| Canary     | Safe gradual roll | Weighted routing | Lambda/ECS weights | Stepwise reduce  |

---
