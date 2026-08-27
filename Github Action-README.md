## GitHub Actions - Interview Notes & Advanced Concepts
##### What is GitHub Actions?

GitHub Actions is a CI/CD and automation platform built into GitHub that allows you to automate software workflows such as:

Build
Test
Deploy
Infrastructure provisioning (Terraform)
Security scanning
Notifications

Workflow files are stored in:
```
.github/workflows/
```
Example: 
```
name: Build Application

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Install Dependencies
        run: npm install

      - name: Run Tests
        run: npm test
```
## GitHub Actions Architecture
```
Workflow
   ↓
Jobs
   ↓
Steps
   ↓
Actions / Commands
```
### Workflow

A YAML configuration file.

Job

A collection of steps executed on the same runner.

Step

Individual task execution.

Action

Reusable automation component.

GitHub Actions Components
1. Workflows
```
name: CI Pipeline
```
A workflow contains one or more jobs.
2. Events (Triggers)
Push Event
```
on:
  push:
```
Pull Request
```
on:
  pull_request:
```
Scheduled Run
```
on:
  schedule:
    - cron: "0 0 * * *"
```
Manual Trigger
```
on:
  workflow_dispatch:
```
### 3. Runners

Runners execute workflows.

GitHub Hosted
```
runs-on: ubuntu-latest
```
Options:
```
ubuntu-latest
windows-latest
macos-latest
```
### Self-Hosted Runner
```
runs-on: self-hosted
```
Used when:

Internal network access required
Custom software installed
Cost optimization
Environment Variables
```
env:
  APP_NAME: terraform-app
```
Access:
```
run: echo $APP_NAME
```
## Secrets Management

Store secrets in:
```
Settings
 → Secrets and Variables
 → Actions
```
use:
```
env:
  CLIENT_ID: ${{ secrets.AZURE_CLIENT_ID }}
```
Never hardcode:
```
password: admin123
```
❌ Bad practice
#### Artifacts

Used to store files between jobs.
```
- uses: actions/upload-artifact@v4
  with:
    name: build-files
    path: dist/
```
Download:
```
- uses: actions/download-artifact@v4
```
Job Dependencies
```
jobs:
  build:

  deploy:
    needs: build
```
Execution:
```
Build
  ↓
Deploy
```
### Matrix Strategy

Run jobs for multiple versions simultaneously.
```
strategy:
  matrix:
    node-version: [18, 20]
```
Example:
```
runs-on: ubuntu-latest

strategy:
  matrix:
    node-version: [18, 20]
```
GitHub runs in parallel.
### Reusable Workflows

Create once and reuse.
```
uses: company/repo/.github/workflows/deploy.yml@main
```
Benefits:

Standardization
Reduced duplication
Easier maintenance

Conditional Execution
```
if: github.ref == 'refs/heads/main'
```
Example:
```
- name: Deploy
  if: github.ref == 'refs/heads/main'
```
Runs only on main branch.

### Terraform with GitHub Actions
Example Pipeline
```
name: Terraform

on:
  push:
    branches:
      - main

jobs:
  terraform:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v4

    - uses: hashicorp/setup-terraform@v3

    - run: terraform init

    - run: terraform validate

    - run: terraform plan

    - run: terraform apply -auto-approve
```
## GitHub Actions for Azure Login
```
- name: Azure Login
  uses: azure/login@v2
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```
### Caching

Improves build speed.
```
- uses: actions/cache@v4
  with:
    path: ~/.npm
    key: npm-${{ hashFiles('package-lock.json') }}
```
Benefits:

Faster execution
Reduced downloads
## GitHub Environments

Example:
```
environment: production
```
Features:

Approval gates
Environment secrets
Deployment tracking

## Common Interview Questions
#### 1. What is GitHub Actions?

GitHub Actions is GitHub's CI/CD platform used to automate build, test, deployment, and infrastructure workflows using YAML-based configurations.

### 2. What is the difference between Workflow, Job, and Step?
Workflow = Entire automation pipeline
Job = Group of related steps
Step = Individual task within a job
### 3. What are GitHub-hosted and Self-hosted runners?

GitHub-hosted

Managed by GitHub
Easy setup
Temporary VM

Self-hosted

Managed by organization
Custom software/network access
Better control
### 4. How do you secure secrets?

Store secrets in GitHub Secrets.

```
${{ secrets.AZURE_CLIENT_ID }}
```

Never hardcode passwords or keys.

### 5. How do you pass data between jobs?

Using artifacts.

```
upload-artifact
2
download-artifact
```
### 6. What is a Matrix Strategy?

Allows running the same job against multiple OS versions, language versions, or environments in parallel.

#### 7. What is needs in GitHub Actions?
```
deploy:
2
needs: build
3

```
Ensures deploy runs only after build succeeds.

### 8. How do you manually trigger a workflow?
```
on:
2
workflow_dispatch:
3
```

Then click Run Workflow in GitHub.

#### 9. Difference between GitHub Actions and Jenkins?
### GitHub Actions vs Jenkins
| Feature | GitHub Actions | Jenkins |
| :--- | :--- | :--- |
| **Integration** | Built into GitHub | Separate server |
| **Maintenance** | Minimal maintenance | Server administration required |
| **Configuration** | YAML-based | Pipeline scripts (Groovy) |
| **Ecosystem** | GitHub-native | Tool agnostic |


### 10. Explain a Terraform CI/CD Pipeline in GitHub Actions.

```
Developer Commit
        ↓
GitHub Action Trigger
        ↓
Terraform Init
        ↓
Terraform Validate
        ↓
Terraform Plan
        ↓
Approval
        ↓
Terraform Apply
        ↓
Deployment Complete
```
## GitHub Actions CI/CD Flow (End-to-End)

A typical GitHub Actions CI/CD pipeline automates the process from code commit to production deployment.

High-Level Flow
```
Developer Code Commit
          │
          ▼
     Git Push/PR
          │
          ▼
 GitHub Actions Trigger
          │
          ▼
      Checkout Code
          │
          ▼
 Build Application
          │
          ▼
 Run Unit Tests
          │
          ▼
 Security/Code Scan
          │
          ▼
 Create Artifact
          │
          ▼
 Deploy to Dev
          │
          ▼
 Integration Testing
          │
          ▼
 Approval Gate
          │
          ▼
 Deploy to Production
          │
          ▼
 Monitoring & Validation
```
Sample CI/CD Workflow
```
name: CI-CD Pipeline

on:
  push:
    branches:
      - main

jobs:

  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Source
        uses: actions/checkout@v4

      - name: Setup Java
        uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'temurin'

      - name: Build Application
        run: mvn clean package

      - name: Upload Artifact
        uses: actions/upload-artifact@v4
        with:
          name: app-build
          path: target/*.jar

  deploy-dev:
    needs: build
    runs-on: ubuntu-latest

    steps:
      - name: Download Artifact
        uses: actions/download-artifact@v4
        with:
          name: app-build

      - name: Deploy to Dev
        run: echo "Deploying to Dev Environment"

  deploy-prod:
    needs: deploy-dev
    runs-on: ubuntu-latest
    environment: production

    steps:
      - name: Deploy to Production
        run: echo "Deploying to Production"
```
Terraform CI/CD Flow Using GitHub Actions

Most DevOps interviews focus on this scenario.

Flow
```
Developer Changes Terraform Code
               │
               ▼
          Pull Request
               │
               ▼
        Terraform fmt
               │
               ▼
      Terraform validate
               │
               ▼
        Terraform plan
               │
               ▼
     Review & Approval
               │
               ▼
          Merge Main
               │
               ▼
         Terraform Apply
               │
               ▼
    Infrastructure Created
```
Example Workflow
```
name: Terraform Deployment

on:
  push:
    branches:
      - main

jobs:
  terraform:

    runs-on: ubuntu-latest

    steps:

      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Setup Terraform
        uses: hashicorp/setup-terraform@v3

      - name: Terraform Init
        run: terraform init

      - name: Terraform Validate
        run: terraform validate

      - name: Terraform Plan
        run: terraform plan

      - name: Terraform Apply
        run: terraform apply -auto-approve
```
Azure Deployment CI/CD Flow
```
Developer Commit
        │
        ▼
Build Application
        │
        ▼
Run Tests
        │
        ▼
Create Artifact
        │
        ▼
Azure Login
        │
        ▼
Deploy to App Service/AKS
        │
        ▼
Smoke Test
        │
        ▼
Production Release
```
Example Azure Login:
```
- name: Azure Login
  uses: azure/login@v2
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```
### Best Practices
Branch Strategy
```
feature/*
    │
    ▼
develop
    │
    ▼
main
```
Use Environment Approval
```
environment: production
```
Before production deployment:
```
Approve Deployment
       │
       ▼
Deploy
```
Store Secrets Securely
```
${{ secrets.AZURE_CLIENT_ID }}
${{ secrets.AWS_ACCESS_KEY_ID }}
```

Never hardcode credentials.
### Interview Answer (2-Minute)

"In GitHub Actions CI/CD, a workflow is triggered when code is pushed or a pull request is created. The pipeline checks out the code, performs build and test stages, runs security and quality scans, generates artifacts, and stores them. After successful validation, the deployment stage is triggered. For production environments, approval gates can be configured using GitHub Environments. In Terraform projects, the pipeline typically runs terraform fmt, validate, plan, and apply. Secrets are securely stored in GitHub Secrets, and deployments can run on GitHub-hosted or self-hosted runners."
