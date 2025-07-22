### 🧪 Lab: Use S3 to Manage and Version Shared CloudFormation Template Modules

---

## 📌 Objective

Learn how to **store**, **version**, and **reuse** CloudFormation template modules (like VPC, Security, EC2, etc.) in an S3 bucket. Reference specific versions in your main stack for **repeatable, modular deployments**.

---

## 🪣 1. Organize and Version Template Modules in S3

1. **Structure your local folder like:**

   ```
   cloudformation-modules/
   ├── v1/
   │   ├── vpc-stack.yaml
   │   ├── security-stack.yaml
   │   └── ec2-stack.yaml
   └── v2/
       ├── vpc-stack.yaml
       ├── security-stack.yaml
       └── ec2-stack.yaml
   ```
2. **Upload versioned modules to S3:**

   ```bash
   aws s3 cp cloudformation-modules/v1/ s3://YOUR_BUCKET/modules/v1/ --recursive
   aws s3 cp cloudformation-modules/v2/ s3://YOUR_BUCKET/modules/v2/ --recursive
   ```

---

## 🧱 2. Reference a Specific Module Version in Main Stack

Example main stack referencing **version v1** modules from S3:

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: Parent stack using versioned modules from S3

Parameters:
  ModuleVersion:
    Type: String
    Default: v1
    AllowedValues: [v1, v2]

Resources:
  VPCStack:
    Type: AWS::CloudFormation::Stack
    Properties:
      TemplateURL: !Sub "https://s3.amazonaws.com/YOUR_BUCKET/modules/${ModuleVersion}/vpc-stack.yaml"

  SecurityStack:
    Type: AWS::CloudFormation::Stack
    Properties:
      TemplateURL: !Sub "https://s3.amazonaws.com/YOUR_BUCKET/modules/${ModuleVersion}/security-stack.yaml"
      Parameters:
        VpcId: !GetAtt VPCStack.Outputs.VpcId

  EC2Stack:
    Type: AWS::CloudFormation::Stack
    Properties:
      TemplateURL: !Sub "https://s3.amazonaws.com/YOUR_BUCKET/modules/${ModuleVersion}/ec2-stack.yaml"
      Parameters:
        SubnetId: !GetAtt VPCStack.Outputs.PublicSubnetId
        SecurityGroupId: !GetAtt SecurityStack.Outputs.SecurityGroupId
```

---

## 🚀 Deploy Using a Specific Module Version

Deploy using `v1`:

```bash
aws cloudformation deploy \
  --template-file main-stack.yaml \
  --stack-name my-modular-stack \
  --parameter-overrides ModuleVersion=v1
```

Deploy using `v2`:

```bash
aws cloudformation deploy \
  --template-file main-stack.yaml \
  --stack-name my-modular-stack \
  --parameter-overrides ModuleVersion=v2
```

---

## ✅ Key Concepts Practiced

| Concept                 | Used in This Lab                                   |
| ----------------------- | -------------------------------------------------- |
| S3 for template storage | Versioning modules in S3 bucket                    |
| Modularization          | Separate YAMLs for VPC, Security, EC2              |
| Nested stacks           | `AWS::CloudFormation::Stack` with S3 `TemplateURL` |
| Parameterized version   | Choose module version at deploy time               |
| Repeatability           | Reuse and rollback with module versioning          |

---

## 📘 Best Practices

* Keep modules in versioned folders for easy rollback/audit.
* Use parameters in the parent stack for dynamic module selection.
* Store all templates in S3 with clear paths and naming.
* Use IAM policies to control who can update templates in S3.

Ready for the next lab.
