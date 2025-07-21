## 🧪 Lab: Build a Complex CloudFormation Template Using Intrinsic Functions & Conditions

### ✅ Objective:

Deploy a **highly reusable and environment-aware** CloudFormation stack that:

* Creates an EC2 instance **only in the 'prod' environment**
* Uses **Mappings**, **Conditions**, **Parameters**, **Outputs**
* Utilizes **advanced intrinsic functions**: `Fn::Sub`, `Fn::If`, `Fn::Equals`, `Fn::Join`, `Fn::FindInMap`, `Ref`

---

## 🔧 Step-by-Step Setup

### 🪄 1. Template Overview

This template will:

* Accept environment (`dev`, `prod`) as parameter
* Create a key pair and security group
* Launch an EC2 instance *only* if the environment is `prod`
* Use `Mappings` to define AMI per region
* Output instance ID conditionally

---

### 📁 Template: `ec2-with-conditions.yaml`

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: >
  🏗️ Complex CloudFormation Template using Intrinsic Functions, Mappings, Conditions

Parameters:
  Environment:
    Type: String
    Description: Deployment environment
    AllowedValues:
      - dev
      - prod
    Default: dev

  InstanceType:
    Type: String
    Default: t2.micro
    AllowedValues:
      - t2.micro
      - t3.micro
      - t3.small
    Description: EC2 instance type

Mappings:
  RegionMap:
    us-east-1:
      AMI: ami-0c02fb55956c7d316
    ap-south-1:
      AMI: ami-0f5ee92e2d63afc18

Conditions:
  IsProd: !Equals [ !Ref Environment, prod ]

Resources:

  MyKeyPair:
    Type: AWS::EC2::KeyPair
    Properties:
      KeyName: !Sub "${Environment}-key"

  MySecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Allow SSH
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 22
          ToPort: 22
          CidrIp: 0.0.0.0/0

  EC2Instance:
    Type: AWS::EC2::Instance
    Condition: IsProd
    Properties:
      InstanceType: !Ref InstanceType
      KeyName: !Ref MyKeyPair
      ImageId: !FindInMap [RegionMap, !Ref "AWS::Region", AMI]
      SecurityGroupIds:
        - !Ref MySecurityGroup
      Tags:
        - Key: Name
          Value: !Sub "${Environment}-EC2Instance"

Outputs:
  InstanceId:
    Condition: IsProd
    Description: Instance ID if deployed
    Value: !Ref EC2Instance

  KeyName:
    Description: The key name used for EC2
    Value: !Ref MyKeyPair
```

---

## ▶️ How to Deploy

```bash
aws cloudformation deploy \
  --template-file ec2-with-conditions.yaml \
  --stack-name complex-ec2-stack \
  --parameter-overrides Environment=prod InstanceType=t3.micro \
  --capabilities CAPABILITY_NAMED_IAM
```

🔁 Try also with `Environment=dev` to test condition skipping EC2 creation.

---

## ✅ Key Concepts Practiced

| Concept                  | Used in Template                    |
| ------------------------ | ----------------------------------- |
| `Conditions`             | `IsProd` to control EC2 creation    |
| `Mappings`               | AMI per region                      |
| `Fn::Sub`                | Dynamic names/tags                  |
| `Fn::FindInMap`          | Regional AMI lookup                 |
| `Fn::Equals`, `Ref`      | Used in `Conditions`                |
| `Outputs with Condition` | Only shows output if EC2 is created |

---

## 💡 Real-World Use Case

In production, you often:

* Want to restrict resource creation in lower environments (like EC2 or RDS)
* Need regional values like AMIs (via Mappings)
* Create different behaviors or resource settings based on environment

---

## 📘 Best Practices Followed

* ✅ Use of `Mappings` instead of hardcoding AMIs
* ✅ Conditional resource deployment
* ✅ Parameterization for reusability
* ✅ Clear and scoped tagging
* ✅ Output values for automation use