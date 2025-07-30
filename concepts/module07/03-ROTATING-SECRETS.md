# 🔄 Rotating Secrets & Integrating with Application Deployments

---

## 🔑 **Why Rotate Secrets?**

* **Reduces risk** if credentials are leaked or compromised.
* **Enables compliance** with security standards (PCI, SOC2, etc.).
* **Prevents stale or shared secrets** across environments.

---

## 🔄 **Rotating Secrets in AWS**

### **1. Secrets Manager Native Rotation**

* **AWS Secrets Manager** supports automatic rotation for many secret types (e.g., RDS, API keys).
* Configure a Lambda function (built-in or custom) as a rotation handler.
* **How to Enable:**

  * Console or CloudFormation (`AWS::SecretsManager::RotationSchedule`).
  * Set rotation interval (e.g., 30 days).

**Example:**

```yaml
Resources:
  MySecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Name: myapp/dbpassword

  MyRotationSchedule:
    Type: AWS::SecretsManager::RotationSchedule
    Properties:
      SecretId: !Ref MySecret
      RotationLambdaARN: arn:aws:lambda:us-east-1:123456789012:function:my-rotation-lambda
      RotationRules:
        AutomaticallyAfterDays: 30
```

---

### **2. SSM Parameter Store Rotation**

* **No native rotation**—rotate values via automation (Lambda, Step Functions, or CI/CD jobs).
* Update parameter values programmatically and ensure apps use latest.

---

## 🚀 **Integrating Rotated Secrets with App Deployments**

* **Use dynamic references** (`{{resolve:secretsmanager:...}}` or `{{resolve:ssm:...}}`) in CloudFormation templates so stacks always fetch latest value at deploy time.
* **Deployments:**

  * On every app or infrastructure deployment, fetch secrets dynamically so the app gets current credentials.
  * Use environment variables, config files, or secret mounts in containers.
* **CI/CD Pipelines:**

  * Trigger deployment or restart on secret rotation.
  * Automate rollouts when a new secret version is published.

**Example – Lambda Function Using Rotated Secret:**

```yaml
Environment:
  Variables:
    DB_PASSWORD: !Sub "{{resolve:secretsmanager:myapp/dbpassword:SecretString:password}}"
```

---

## 📝 **Best Practices**

* Automate secret rotation for all sensitive credentials (databases, API keys).
* Notify and/or trigger application redeploys or restarts when secrets rotate.
* Audit access and rotation events (CloudTrail, Secrets Manager logs).
* Use minimal IAM permissions for apps/services that consume secrets.

---

## ✨ **Pro Tip**

Integrate secret rotation with CI/CD for truly hands-off security—your apps always use the latest secret, with no manual updates needed! 🔒🚀
