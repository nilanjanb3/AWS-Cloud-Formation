## **MODULE 1: Advanced CloudFormation Core Concepts**

### **Concepts**

* [Deep dive into Stacks, Templates, Parameters, Resources, Outputs, Mappings, Conditions, Intrinsic Functions (Fn::Sub, Fn::If, etc.)](./concepts/module01/01-CORE-CONCEPTS.md)
* [YAML vs. JSON best practices](./concepts/module01/02-YAML-VS-JSON.md)
* [Stack lifecycle and dependencies](./concepts/module01/03-STACK-LIFECYCLE.MD)

### **Hands-On**

* [Build a complex template using advanced intrinsic functions and conditional logic](./handson/module01/01-COMPLEX-TEMPLATE.md)
* [Use mappings and outputs to pass data between stacks](./handson/module01/02-MAPPING-AND-OUTPUT.md)

### **Practical Use Cases**

* Dynamically configure resources (e.g., multi-region deployment configs)
* Enforce environment-specific settings (Dev, QA, Prod)
* Use Outputs for sharing resource IDs across teams/stacks

---

## **MODULE 2: Modularization and Reusability**

### **Concepts**

* [Nested stacks, StackSets, and cross-stack references](./concepts/module02/01-NESTED-SET-CROSS.md)
* [Template modularization using S3, YAML anchors, and macros](./concepts/module02/02-TEMPLATE-MODULARIZATION.md)
* [DRY (Don’t Repeat Yourself) with reusable modules](./concepts/module02/03-DRY.md)

### **Hands-On**

* [Refactor a monolithic template into multiple nested stacks](./handson/module02/01-REFRACTOR.md)
* [Use S3 to manage and version shared template modules](./handson/module02/02-STACK-VESIONING.md)

### **Practical Use Cases**

* Maintain a library of standard templates for VPCs, IAM roles, etc.
* Share and reuse network/security baselines across projects and accounts

---

## **MODULE 3: Modern IaC & CI/CD Integration**

### **Concepts**

* [CI/CD patterns for CloudFormation (Blue/Green, Canary)](./concepts/module03/01-CICD-PATTERNS.md)
* [Version control, template testing (cfn-lint, taskcat), and review workflows](./concepts/module03/02-VERSIONING-TEST.md)
* [Automated rollback and drift detection](./concepts/module03/03-DRIFT-DETECTION-AND-ROLLBACK.md)

### **Hands-On**

* [Set up a GitHub Actions or CodePipeline flow to lint, validate, and deploy templates](./handson/module03/01-CICD.md)
* [Simulate and detect configuration drift](./handson/module03/02-DRIFT.md)

### **Practical Use Cases**

* Automated, auditable infrastructure changes for production workloads
* Compliance enforcement and team collaboration via pull requests

---

## **MODULE 4: Advanced Resource Types & Custom Resources**

### **Concepts**

* Managing advanced AWS resources (EKS, ECS, Lambda, ALB/NLB, etc.)
* Lambda-backed custom resources: When to use and how to implement
* Integration with AWS Marketplace resources

### **Hands-On**

* Write a template to deploy EKS or ECS with supporting resources (e.g., IAM, Security Groups)
* Create a Lambda-backed custom resource to automate a non-native AWS operation

### **Practical Use Cases**

* Automated provisioning of complex multi-tier apps (e.g., container clusters, serverless APIs)
* Integrating 3rd-party SaaS or compliance checks into stack lifecycle

---

## **MODULE 5: Security, Compliance & Governance**

### **Concepts**

* IAM policy generation and least-privilege patterns
* Stack policies, Service Control Policies (SCP), resource-level controls
* Tagging enforcement, cost allocation, compliance audits

### **Hands-On**

* Implement IAM with scoped-down policies via CloudFormation
* Set up stack policies to protect critical resources

### **Practical Use Cases**

* Automated enforcement of organization-wide security baselines
* Cost reporting and allocation tagging for chargeback/showback

---

## **MODULE 6: Change Management & Stack Operations**

### **Concepts**

* Change sets: Previewing and approving stack updates
* Stack update behaviors, rollbacks, update policies
* Deletion policies, resource protection

### **Hands-On**

* Apply a stack update with change sets and perform a controlled rollback
* Use deletion policy to retain data after resource removal

### **Practical Use Cases**

* Minimize risk of production outages during infrastructure updates
* Safeguard stateful resources like databases with retention policies

---

## **MODULE 7: Secrets Management & Dynamic References**

### **Concepts**

* Using SSM Parameter Store and Secrets Manager
* Dynamic references for secure value injection at deploy time
* Rotating secrets and integrating with application deployments

### **Hands-On**

* Reference secrets/parameters in templates for DB passwords, API keys
* Rotate a secret and update stack without code changes

### **Practical Use Cases**

* Zero-hardcoded credentials in templates
* Automated secrets rotation and application update workflows

---

## **MODULE 8: Macros, Transforms & Customization**

### **Concepts**

* AWS::Include, macros, and custom pre-processors
* AWS::Serverless transform for serverless applications
* Writing and deploying custom macros (e.g., for tagging, VPCs)

### **Hands-On**

* Create a macro to standardize tagging or resource creation
* Use AWS SAM transform to deploy a serverless REST API

### **Practical Use Cases**

* Organization-wide enforcement of CloudFormation standards
* Accelerate serverless adoption with reusable patterns

---

## **MODULE 9: Monitoring, Logging & Troubleshooting**

### **Concepts**

* Stack events, SNS/EventBridge for notifications
* Logging stack operations (CloudTrail, Config)
* Debugging failed stacks (events, logs, resource status)

### **Hands-On**

* Configure SNS notifications for stack changes/failures
* Audit stack changes via CloudTrail and AWS Config

### **Practical Use Cases**

* Real-time alerting on production infrastructure changes
* Root-cause analysis for failed deployments

---

## **MODULE 10: Cross-Account, Multi-Region & Enterprise Use**

### **Concepts**

* StackSets for multi-account/multi-region deployment
* Integration with Control Tower, Service Catalog
* Guardrails and organization-wide governance

### **Hands-On**

* Deploy a StackSet to multiple accounts/regions
* Enforce organization rules with Service Control Policies

### **Practical Use Cases**

* Consistent, compliant infrastructure provisioning at scale
* Enterprise landing zones and standardized service rollout

---

## **MODULE 11: Real-World Projects**

### **Project 1: Enterprise VPC Factory**

* **Concept:** Modular VPC template with networking, logging, and endpoints
* **Hands-On:** Build parameterized nested stacks for VPC, subnets, NAT, IGW, endpoints
* **Use Case:** Quickly spin up compliant VPCs across environments or teams

### **Project 2: Serverless Application Deployment**

* **Concept:** Complete serverless app (API Gateway + Lambda + DynamoDB + S3)
* **Hands-On:** Use AWS::Serverless, custom resources for deployment, CI/CD automation
* **Use Case:** Real-world pattern for event-driven or microservices architectures

### **Project 3: Production EKS/ECS Cluster Automation**

* **Concept:** End-to-end infra-as-code for secure EKS/ECS with supporting resources
* **Hands-On:** Use modular templates, custom resources, and cross-stack references
* **Use Case:** Accelerate container platform adoption with repeatable infra

### **Project 4: Enterprise Logging & Monitoring**

* **Concept:** Cross-account CloudWatch, GuardDuty, and Config using StackSets
* **Hands-On:** Deploy and aggregate logs/metrics across multiple accounts/regions
* **Use Case:** Meet enterprise compliance and audit requirements

### **Project 5: CloudFormation Linter & CI/CD Automation**

* **Concept:** Automated pipeline for template validation, linting, and drift detection
* **Hands-On:** Integrate cfn-lint, taskcat, change sets, and notifications into CI/CD
* **Use Case:** Enforce quality and compliance at scale

---

### **How to Use This Plan**

* Go **module by module**: deep dive into concepts, then do the hands-on, and finally review/implement the practical use cases.
* For each topic, you can ask for:

  * Cheat-sheets
  * Demo templates
  * Scenario-based challenges

Ready for the next step? Just name a module and I’ll expand it into a detailed, step-by-step lab with code and scenarios!
