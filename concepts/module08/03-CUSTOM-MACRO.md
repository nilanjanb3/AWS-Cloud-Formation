# 🪄 Writing & Deploying Custom Macros in CloudFormation (e.g., for Tagging, VPCs)

---

## 🤔 **What Are Custom Macros?**

* **Macros** are Lambda-powered functions that transform CloudFormation templates before AWS processes them.
* Use cases: auto-tagging, custom resource generation, enforcing naming standards, or dynamic VPC/subnet generation.

---

## ⚙️ **How to Write a Custom Macro**

1. **Write a Lambda Function**

   * Receives the CloudFormation template as input.
   * Returns a (possibly modified) template.
   * Handles `Create`, `Update`, and `Delete` events (usually only `Create` and `Update` for transforms).

   **Example (Python, simplified):**

   ```python
   def lambda_handler(event, context):
       template = event['fragment']
       # Add tags, modify resources, etc.
       # Example: auto-inject default tags into all resources
       for res in template.get('Resources', {}):
           template['Resources'][res].setdefault('Properties', {})
           template['Resources'][res]['Properties'].setdefault('Tags', [])
           template['Resources'][res]['Properties']['Tags'].append({'Key': 'Environment', 'Value': 'Production'})
       return {
           "requestId": event['requestId'],
           "status": "success",
           "fragment": template
       }
   ```

2. **Deploy Lambda Function**

   * Package and deploy to AWS Lambda.
   * Grant permissions to CloudFormation service principal.

3. **Register the Macro**

   * Use CloudFormation or Console:

     ```yaml
     Resources:
       MyMacro:
         Type: AWS::CloudFormation::Macro
         Properties:
           Name: AutoTagMacro
           FunctionName: arn:aws:lambda:us-east-1:123456789012:function:autotag-macro
           LogGroupName: /aws/lambda/autotag-macro
     ```
   * Macro name will be used in templates.

---

## 🛠️ **Using Your Macro in Templates**

* Reference in the `Transform` section:

  ```yaml
  Transform: AutoTagMacro
  Resources:
    MyBucket:
      Type: AWS::S3::Bucket
      Properties: {}
  ```
* At deploy time, CloudFormation invokes your macro to pre-process the template.

---

## 🚦 **Best Practices**

* **Keep macro logic efficient** (must run quickly).
* **Log all changes** for debugging/troubleshooting.
* **Version your macro code**—update carefully for live stacks.
* Use for automation or compliance (e.g., always apply tags, enforce resource naming, inject monitoring).

---

## ✨ **Pro Tip**

Macros give you “superpowers” for IaC—enforce standards, reduce manual effort, and keep your AWS infrastructure smart and compliant! 🧙‍♂️
