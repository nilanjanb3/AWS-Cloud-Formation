### 🧪 Lab: Set Up a CI/CD Pipeline to Lint, Validate, and Deploy CloudFormation Templates (GitHub Actions & AWS CodePipeline)

---

## 📌 Objective

Implement a modern pipeline to **automatically lint, validate, and deploy** CloudFormation templates when you push code. Covers both **GitHub Actions** and **AWS CodePipeline** best practices.

---

## 🚩 Prerequisites

* CloudFormation templates stored in a GitHub repo
* AWS CLI configured with deploy permissions
* S3 bucket for storing deployment artifacts

---

## 🚀 Option 1: GitHub Actions CI/CD for CloudFormation

### 1. Create `.github/workflows/cfn-deploy.yaml` in your repo:

```yaml
name: Lint, Validate & Deploy CloudFormation

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  lint-validate-deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout source
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.x'

      - name: Install cfn-lint
        run: pip install cfn-lint

      - name: Lint CloudFormation templates
        run: cfn-lint ./**/*.yaml

      - name: Validate CloudFormation templates
        run: |
          for template in $(find . -name "*.yaml"); do
            aws cloudformation validate-template --template-body file://$template || exit 1
          done

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ap-south-1

      - name: Deploy CloudFormation Stack
        run: |
          aws cloudformation deploy \
            --template-file main-stack.yaml \
            --stack-name ci-cfn-stack \
            --capabilities CAPABILITY_NAMED_IAM
```

---

## 🚀 Option 2: AWS CodePipeline for CloudFormation

### 1. **Upload your templates to an S3 bucket**

### 2. **Create a CodePipeline** with these stages:

* **Source**: GitHub or CodeCommit (fetch code on push)
* **Build**: CodeBuild project to lint & validate
* **Deploy**: CloudFormation deployment action

#### Example `buildspec.yaml` for CodeBuild

```yaml
version: 0.2

phases:
  install:
    runtime-versions:
      python: 3.x
    commands:
      - pip install cfn-lint
  build:
    commands:
      - cfn-lint ./**/*.yaml
      - for template in $(find . -name "*.yaml"); do aws cloudformation validate-template --template-body file://$template; done
artifacts:
  files:
    - '**/*'
```

#### Deploy Stage in CodePipeline

* **Action type:** AWS CloudFormation
* **Template:** The output of the validated template (`main-stack.yaml` from build artifact)
* **Stack name:** `ci-cfn-stack`
* **Capabilities:** `CAPABILITY_NAMED_IAM`

---

## ✅ Key Concepts Practiced

| Concept        | Where Used                                  |
| -------------- | ------------------------------------------- |
| Linting        | `cfn-lint` for static analysis              |
| Validation     | `aws cloudformation validate-template`      |
| GitHub Actions | End-to-end automation on code push          |
| CodePipeline   | Enterprise-level CI/CD with validation step |
| Secure deploy  | Uses GitHub/AWS secrets, IAM best practices |

---

## 📘 Best Practices

* Lint before validation and deployment
* Block deployment on failed lint/validation
* Use secure storage for credentials/secrets
* Always require review/approval for prod deploys
* Keep all CI/CD config in version control

Ready for the next lab.
