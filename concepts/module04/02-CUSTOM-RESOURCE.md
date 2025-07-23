# 🪄 Lambda-Backed Custom Resources: When to Use & How to Implement

---

## ❓ **When to Use Lambda-Backed Custom Resources**

* **What:**
  Use when you need to perform operations during stack actions that CloudFormation doesn’t support natively.
* **Typical Scenarios:**

  * Creating/updating resources not (yet) supported by CloudFormation.
  * Running custom logic: configuring 3rd-party services, setting up external integrations.
  * Generating dynamic resource values at deploy time (e.g., random passwords, custom configs).
* **Real-World Examples:**

  * Registering DNS entries in Route53 from a third-party DNS provider.
  * Onboarding user accounts to external SaaS during stack creation.
  * Auto-generating secrets and storing them in AWS Secrets Manager.

---

## ⚙️ **How to Implement a Lambda-Backed Custom Resource**

### 1. **Create a Lambda Function**

* **Role:**
  Handles `Create`, `Update`, and `Delete` stack events.
* **Input:**
  Receives event data from CloudFormation (ResourceProperties, etc.).
* **Output:**
  Sends a response to a pre-signed S3 URL indicating success or failure.

### 2. **Reference Lambda in the Template**

* Use the `AWS::CloudFormation::CustomResource` or `AWS::CloudFormation::Resource` type.
* Point to the Lambda function’s ARN.
* Pass parameters via `Properties`.

**Example:**

```yaml
Resources:
  MyCustomFunction:
    Type: AWS::Lambda::Function
    Properties:
      Handler: index.handler
      Role: arn:aws:iam::123456789012:role/my-custom-role
      Code:
        S3Bucket: my-bucket
        S3Key: my-custom-code.zip
      Runtime: python3.11

  MyCustomResource:
    Type: Custom::MyCustomResource
    Properties:
      ServiceToken: !GetAtt MyCustomFunction.Arn
      DomainName: "example.com"
      TTL: 60
```

* `ServiceToken`: The ARN of the Lambda function to invoke.

### 3. **Lambda Handler Logic (Simplified)**

```python
def handler(event, context):
    # event['RequestType']: 'Create', 'Update', or 'Delete'
    props = event['ResourceProperties']
    if event['RequestType'] == 'Create':
        # Custom logic here
        send_response(event, context, 'SUCCESS', {"Result": "Created"})
    elif event['RequestType'] == 'Delete':
        # Cleanup logic here
        send_response(event, context, 'SUCCESS', {})
    # send_response sends status back to CloudFormation
```

### 4. **Response Protocol**

* Lambda **must** send a success/failure response to the URL in `event['ResponseURL']`.
* Many use AWS Lambda Powertools or helper libraries to simplify this.

---

## 🏁 **Best Practices**

* **Always handle all event types** (`Create`, `Update`, `Delete`) for idempotency and safe stack deletion.
* **Set timeouts and error handling**—CloudFormation waits for a response.
* **Minimize permissions:** Grant only what the Lambda needs.
* **Log outputs:** Use CloudWatch Logs for debugging custom resources.

---

## ✨ **Pro Tip**

Use Lambda-backed custom resources sparingly—prefer native CloudFormation features when available!
But when you need custom automation during deployments, they’re a powerful tool for making anything possible. 🚀
