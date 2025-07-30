# 🧬 Dynamic References for Secure Value Injection at Deploy Time

---

## 🔍 **What Are Dynamic References?**

* **Dynamic references** let CloudFormation securely fetch sensitive values (like passwords, API keys, config values) from services such as **SSM Parameter Store** or **Secrets Manager** **at deploy time**—not stored in the template or stack history.
* **Why:**
  Avoids exposing secrets or sensitive config in templates, Git, or logs.

---

## 🗂️ **How to Use Dynamic References**

* **Syntax:**
  `{{resolve:ssm:/parameter/name:version}}`
  `{{resolve:secretsmanager:secret-name:SecretString:key}}`
* **Where to use:**

  * In resource properties (e.g., Lambda env vars, DB passwords, config strings)
  * As values for Parameters, Outputs, or direct resource properties

**Example – SSM Parameter Store:**

```yaml
Environment:
  Variables:
    DB_ENDPOINT: !Sub "{{resolve:ssm:/myapp/db/endpoint:1}}"
```

**Example – Secrets Manager:**

```yaml
Environment:
  Variables:
    DB_PASSWORD: !Sub "{{resolve:secretsmanager:MyAppDBPassword:SecretString:password}}"
```

---

## 🛡️ **Benefits**

* **No plain-text secrets** in templates or stack events
* **Centralized management**: Rotate secrets and update parameters in SSM/Secrets Manager—no template update needed
* **Audit and control**: Use IAM policies to control access to secrets/parameters

---

## ⚡ **Best Practices**

* Prefer dynamic references for **all sensitive values** (API keys, passwords, tokens)
* Grant CloudFormation or target service only the minimum IAM permissions needed to read the secret/parameter
* Use version numbers for SSM parameters to lock to a specific value (or omit for latest)

---

## 🚦 **Limitations & Notes**

* Dynamic references **cannot be used** in every field (e.g., resource logical IDs, Condition/Mapping keys)
* Not resolved at template validation time—resolved during stack create/update only

---

## ✨ **Pro Tip**

> Use dynamic references with YAML’s `!Sub` for clean, secure, and flexible templates—no more accidental secret leaks! 🔒
s