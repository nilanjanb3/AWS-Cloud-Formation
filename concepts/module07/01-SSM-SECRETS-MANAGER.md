# 🔑 Using SSM Parameter Store & Secrets Manager in CloudFormation

---

## 🗂️ **AWS SSM Parameter Store**

* **What:**
  Secure storage for configuration data and plain-text/secure strings (like app configs, URLs, ARNs).
* **How to Use in CloudFormation:**

  * Use `AWS::SSM::Parameter` to create parameters.
  * Use `ssm` or `ssm-secure` dynamic references to fetch existing parameters at deployment/runtime.
* **Example – Create a Parameter:**

  ```yaml
  Resources:
    MyAppConfig:
      Type: AWS::SSM::Parameter
      Properties:
        Name: /myapp/db/endpoint
        Type: String
        Value: mydb.example.com
  ```
* **Example – Reference a Parameter:**

  ```yaml
  DBEndpoint: {{resolve:ssm:/myapp/db/endpoint:1}}
  ```

  (or with YAML short form:
  `Value: !Sub "{{resolve:ssm:/myapp/db/endpoint:1}}"`)

---

## 🗝️ **AWS Secrets Manager**

* **What:**
  Manages sensitive data (passwords, API keys, tokens) securely, with automatic rotation and fine-grained access controls.
* **How to Use in CloudFormation:**

  * Use `AWS::SecretsManager::Secret` to create secrets.
  * Use `secretsmanager` dynamic references to fetch secret values.
* **Example – Create a Secret:**

  ```yaml
  Resources:
    DBSecret:
      Type: AWS::SecretsManager::Secret
      Properties:
        Name: MyAppDBPassword
        Description: "RDS DB password"
        SecretString: '{"username":"admin","password":"SuperSecretP@ssw0rd"}'
  ```
* **Example – Reference a Secret Value:**

  ```yaml
  DBPassword: {{resolve:secretsmanager:MyAppDBPassword:SecretString:password}}
  ```

  (or with YAML:
  `Value: !Sub "{{resolve:secretsmanager:MyAppDBPassword:SecretString:password}}"`)

---

## 🚦 **Best Practices**

* **Never hardcode secrets or passwords** in templates—always use SSM Parameter Store or Secrets Manager for sensitive data.
* Use IAM policies to control who/what can access each parameter or secret.
* Use Parameter Store for general configuration; use Secrets Manager for sensitive, rotated credentials.
* Version and document your parameters/secrets for troubleshooting and audits.

---

## ✨ **Pro Tip**

Combine dynamic references with CloudFormation Parameters for secure, automated, and flexible deployments—no secrets exposed in code or logs! 🕵️‍♂️
