### 🧪 Lab: Use Mappings and Outputs to Pass Data Between CloudFormation Stacks

---

## 📌 Objective

Use **stack outputs** and **mappings** to share configuration values between two stacks using **export/import** mechanism (`Export`, `Fn::ImportValue`) — a common pattern in **modular CloudFormation templates**.

---

## 🧱 Stack 1 – Network Stack (`network-stack.yaml`)

Defines a VPC and exports key values like VPC ID and Subnet ID using `Outputs`.

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: Network Stack with VPC and Subnet

Resources:
  MyVPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
      Tags:
        - Key: Name
          Value: my-vpc

  PublicSubnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref MyVPC
      CidrBlock: 10.0.1.0/24
      AvailabilityZone: !Select [ 0, !GetAZs '' ]
      Tags:
        - Key: Name
          Value: my-public-subnet

Outputs:
  VpcId:
    Value: !Ref MyVPC
    Export:
      Name: VPC-ID

  PublicSubnetId:
    Value: !Ref PublicSubnet
    Export:
      Name: Public-Subnet-ID
```

Deploy it:

```bash
aws cloudformation deploy \
  --template-file network-stack.yaml \
  --stack-name network-stack
```

---

## 🧱 Stack 2 – Application Stack (`app-stack.yaml`)

Uses `Fn::ImportValue` to import VPC and Subnet ID, and `Mappings` to choose AMI based on region.

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: Application Stack importing network resources

Mappings:
  RegionMap:
    us-east-1:
      AMI: ami-0c02fb55956c7d316
    ap-south-1:
      AMI: ami-0f5ee92e2d63afc18

Resources:
  AppInstance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: !FindInMap [RegionMap, !Ref "AWS::Region", AMI]
      InstanceType: t2.micro
      NetworkInterfaces:
        - AssociatePublicIpAddress: true
          DeviceIndex: 0
          SubnetId: !ImportValue Public-Subnet-ID
          GroupSet: []
      Tags:
        - Key: Name
          Value: my-app-instance

Outputs:
  ImportedVpc:
    Description: VPC imported from network stack
    Value: !ImportValue VPC-ID
```

Deploy it:

```bash
aws cloudformation deploy \
  --template-file app-stack.yaml \
  --stack-name app-stack
```

---

## ✅ Key Concepts Practiced

| Concept           | Example Used                               |
| ----------------- | ------------------------------------------ |
| `Mappings`        | Region-wise AMI selection                  |
| `Outputs`         | Exported VPC and Subnet IDs                |
| `Export`/`Import` | Cross-stack reference between network/app  |
| `Fn::FindInMap`   | Used to dynamically select AMI             |
| `Fn::ImportValue` | Used to pull VPC/Subnet ID into next stack |

---

## 📘 Best Practices

* Split infrastructure into **modular stacks**
* Use `Export` and `ImportValue` instead of hardcoding IDs
* Keep mappings for **region-specific values**
* Keep outputs well-named and consistent across stacks

Ready for the next lab.
