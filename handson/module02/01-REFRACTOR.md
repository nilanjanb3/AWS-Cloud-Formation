### 🧪 Lab: Refactor a Monolithic CloudFormation Template into Multiple Nested Stacks

---

## 📌 Objective

Take a single monolithic template that provisions a **VPC**, **Security Group**, and **EC2 Instance**, and split it into **modular nested stacks** using `AWS::CloudFormation::Stack` resources.

---

## 🧱 1. Parent Template (`main-stack.yaml`)

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: Root stack that references nested VPC, Security, and EC2 stacks

Parameters:
  Environment:
    Type: String
    Default: dev
    AllowedValues: [dev, prod]

Resources:
  VPCStack:
    Type: AWS::CloudFormation::Stack
    Properties:
      TemplateURL: https://s3.amazonaws.com/YOUR_BUCKET/vpc-stack.yaml

  SecurityStack:
    Type: AWS::CloudFormation::Stack
    Properties:
      TemplateURL: https://s3.amazonaws.com/YOUR_BUCKET/security-stack.yaml
      Parameters:
        VpcId: !GetAtt VPCStack.Outputs.VpcId

  EC2Stack:
    Type: AWS::CloudFormation::Stack
    Properties:
      TemplateURL: https://s3.amazonaws.com/YOUR_BUCKET/ec2-stack.yaml
      Parameters:
        SubnetId: !GetAtt VPCStack.Outputs.PublicSubnetId
        SecurityGroupId: !GetAtt SecurityStack.Outputs.SecurityGroupId
        Environment: !Ref Environment
```

---

## 🧱 2. VPC Stack (`vpc-stack.yaml`)

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: Creates VPC and Public Subnet

Resources:
  MyVPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
      Tags:
        - Key: Name
          Value: nested-vpc

  PublicSubnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref MyVPC
      CidrBlock: 10.0.1.0/24
      AvailabilityZone: !Select [ 0, !GetAZs '' ]
      Tags:
        - Key: Name
          Value: nested-subnet

Outputs:
  VpcId:
    Value: !Ref MyVPC
  PublicSubnetId:
    Value: !Ref PublicSubnet
```

---

## 🧱 3. Security Stack (`security-stack.yaml`)

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: Creates Security Group

Parameters:
  VpcId:
    Type: String

Resources:
  MySecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Allow SSH
      VpcId: !Ref VpcId
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 22
          ToPort: 22
          CidrIp: 0.0.0.0/0

Outputs:
  SecurityGroupId:
    Value: !Ref MySecurityGroup
```

---

## 🧱 4. EC2 Stack (`ec2-stack.yaml`)

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: Launches EC2 instance

Parameters:
  SubnetId:
    Type: String
  SecurityGroupId:
    Type: String
  Environment:
    Type: String

Mappings:
  RegionMap:
    us-east-1:
      AMI: ami-0c02fb55956c7d316
    ap-south-1:
      AMI: ami-0f5ee92e2d63afc18

Resources:
  MyInstance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: t2.micro
      ImageId: !FindInMap [RegionMap, !Ref "AWS::Region", AMI]
      SubnetId: !Ref SubnetId
      SecurityGroupIds:
        - !Ref SecurityGroupId
      Tags:
        - Key: Name
          Value: !Sub "${Environment}-nested-ec2"
```

---

## 📦 Upload to S3

All nested templates must be uploaded to an S3 bucket:

```bash
aws s3 cp vpc-stack.yaml s3://YOUR_BUCKET/
aws s3 cp security-stack.yaml s3://YOUR_BUCKET/
aws s3 cp ec2-stack.yaml s3://YOUR_BUCKET/
```

---

## 🚀 Deploy the Parent Stack

```bash
aws cloudformation deploy \
  --template-file main-stack.yaml \
  --stack-name nested-root-stack \
  --parameter-overrides Environment=prod \
  --capabilities CAPABILITY_NAMED_IAM
```

---

## ✅ Key Concepts Practiced

| Concept                | Used in This Lab                         |
| ---------------------- | ---------------------------------------- |
| Nested stacks          | `AWS::CloudFormation::Stack`             |
| Modularization         | VPC, Security, EC2 in separate templates |
| Parameter Passing      | Between parent and child stacks          |
| Outputs for references | `!GetAtt ChildStack.Outputs.OutputKey`   |
| Mappings + Sub + Ref   | For dynamic region-based values          |

---

## 📘 Best Practices

* Each template does **one job** (VPC, Security, EC2)
* Stack parameters and outputs are **clearly defined**
* Use **S3 URLs** for child templates to keep parent clean
* Keep naming, tagging, and regions configurable via parameters

Ready for the next lab.
