### 🧪 Lab: Write a CloudFormation Template to Deploy EKS (Kubernetes) with IAM and Security Groups

---

## 📌 Objective

Provision a **basic Amazon EKS (Kubernetes)** cluster with supporting **IAM roles** and **security groups** using CloudFormation.
*EKS is the preferred example for modern, industry-standard practice.*

---

## 📁 Template: `eks-minimal-stack.yaml`

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: Minimal EKS Cluster with IAM Role and Security Group

Parameters:
  ClusterName:
    Type: String
    Default: eks-demo
  KubernetesVersion:
    Type: String
    Default: "1.29"
    AllowedValues: ["1.27", "1.28", "1.29"]

Resources:
  EKSClusterRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: "2012-10-17"
        Statement:
          - Effect: Allow
            Principal:
              Service: eks.amazonaws.com
            Action: sts:AssumeRole
      ManagedPolicyArns:
        - arn:aws:iam::aws:policy/AmazonEKSClusterPolicy

  EKSClusterSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Cluster communication with worker nodes
      VpcId: !ImportValue VPC-ID    # Assumes VPC exists and exported
      Tags:
        - Key: Name
          Value: !Sub "${ClusterName}-sg"

  EKSCluster:
    Type: AWS::EKS::Cluster
    Properties:
      Name: !Ref ClusterName
      Version: !Ref KubernetesVersion
      RoleArn: !GetAtt EKSClusterRole.Arn
      ResourcesVpcConfig:
        SecurityGroupIds:
          - !Ref EKSClusterSecurityGroup
        SubnetIds:
          - !ImportValue Public-Subnet-ID    # Assumes subnet exported
      Tags:
        - Key: Environment
          Value: demo

Outputs:
  EKSClusterName:
    Value: !Ref EKSCluster
    Export:
      Name: EKS-Cluster-Name
  EKSClusterSecurityGroup:
    Value: !Ref EKSClusterSecurityGroup
    Export:
      Name: EKS-Cluster-SG
  EKSClusterRole:
    Value: !Ref EKSClusterRole
    Export:
      Name: EKS-Cluster-Role
```

---

## 🚦 Notes

* **Assumes a VPC and public subnet already exist and are exported as `VPC-ID` and `Public-Subnet-ID`** (see previous network stack lab).
* Add a **node group** template for worker nodes if needed (can be a separate nested stack).
* Change security group/VPC references if needed for your account.

---

## 🚀 Deploy

```bash
aws cloudformation deploy \
  --template-file eks-minimal-stack.yaml \
  --stack-name eks-minimal \
  --capabilities CAPABILITY_IAM
```

---

## ✅ Key Concepts Practiced

| Concept       | Used in This Lab                 |
| ------------- | -------------------------------- |
| EKS resource  | `AWS::EKS::Cluster`              |
| IAM roles     | Cluster service role             |
| SecurityGroup | For control plane comms          |
| Cross-stack   | Import VPC and subnet from other |
| Parameters    | Cluster name, K8s version        |

---

## 📘 Best Practices

* Separate network and EKS stacks for reusability
* Use IAM managed policies for EKS roles
* Tag all resources
* Parameterize names and versions

Ready for the next lab. If you want an **ECS** (Fargate/EC2) example instead, let me know.
