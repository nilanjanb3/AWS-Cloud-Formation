### 🧪 Lab: Lambda-Backed Custom Resource for Automated Slack Notification on Stack Events

---

## 📌 Problem Statement

**Real-world Scenario:**
Many organizations want to send notifications (e.g., to Slack, Microsoft Teams, etc.) when a CloudFormation stack is created, updated, or deleted. There’s no built-in CloudFormation resource to send Slack messages.

**Goal:**
Create a **Lambda-backed custom resource** in CloudFormation that automatically sends a **Slack notification** when a stack event (Create/Update/Delete) occurs.

---

## 🛠️ Components

* **Lambda function:** Sends messages to a Slack webhook.
* **IAM Role:** Allows Lambda to run.
* **Custom Resource:** Triggers Lambda during stack events.
* **Parameter:** Slack webhook URL.

---

## 📁 Template: `slack-notify-custom-resource.yaml`

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: Lambda-backed custom resource to send Slack notifications on stack events

Parameters:
  SlackWebhookUrl:
    Type: String
    Description: Slack Webhook URL for notifications

Resources:

  LambdaRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: "2012-10-17"
        Statement:
          - Effect: Allow
            Principal:
              Service: lambda.amazonaws.com
            Action: sts:AssumeRole
      ManagedPolicyArns:
        - arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole

  SlackNotifierFunction:
    Type: AWS::Lambda::Function
    Properties:
      Handler: index.lambda_handler
      Runtime: python3.12
      Timeout: 30
      Role: !GetAtt LambdaRole.Arn
      Environment:
        Variables:
          SLACK_WEBHOOK_URL: !Ref SlackWebhookUrl
      Code:
        ZipFile: |
          import os
          import urllib.request
          import json

          def lambda_handler(event, context):
              webhook_url = os.environ['SLACK_WEBHOOK_URL']
              stack_event = event['RequestType']
              stack_name = event['StackId'].split('/')[1]
              message = f"Stack *{stack_name}* event: *{stack_event}*"
              
              data = json.dumps({"text": message}).encode("utf-8")
              req = urllib.request.Request(
                  webhook_url,
                  data=data,
                  headers={"Content-Type": "application/json"}
              )
              urllib.request.urlopen(req)

              # Must respond to the pre-signed URL so CFN does not hang
              import cfnresponse
              cfnresponse.send(event, context, cfnresponse.SUCCESS, {}, None)

  SlackNotifyCustomResource:
    Type: Custom::SlackNotifier
    Properties:
      ServiceToken: !GetAtt SlackNotifierFunction.Arn

Outputs:
  SlackNotificationStatus:
    Description: Slack notification custom resource executed
    Value: "Slack notification sent on stack event"
```

---

## 🔎 Additional Setup Required

* Add the [**cfnresponse** module](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-lambda-function-code-cfnresponsemodule.html) to the Lambda deployment package (not included in `ZipFile` inline code).
  *For production: Build/deploy Lambda separately with dependencies or use a Lambda Layer for `cfnresponse`.*

---

## 🚦 Deploy

```bash
aws cloudformation deploy \
  --template-file slack-notify-custom-resource.yaml \
  --stack-name notify-stack \
  --parameter-overrides SlackWebhookUrl=https://hooks.slack.com/services/XXXX/YYYY/ZZZZ
  --capabilities CAPABILITY_NAMED_IAM
```

---

## ✅ Key Concepts Practiced

| Concept         | Usage                                    |
| --------------- | ---------------------------------------- |
| Lambda-backed   | Automate non-native operation (Slack)    |
| Custom Resource | Triggers Lambda on stack events          |
| IAM Role        | For Lambda execution                     |
| Parameters      | Secure, runtime-configurable webhook URL |
| Outputs         | Shows custom resource status             |

---

## 📘 Best Practices

* Always **respond** to CloudFormation with `cfnresponse` to avoid stuck stacks
* Never hardcode secrets in templates; use parameters or Secrets Manager
* Use a minimal IAM policy for Lambda
* Log Lambda events for troubleshooting

This pattern can be adapted to automate **any operation** not natively supported by CloudFormation, such as:

* DNS record updates in non-Route53 providers
* Sending emails or SMS on stack actions
* Integrating with ServiceNow/Jira/etc.

Ready for the next lab.
