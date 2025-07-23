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
