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


If you want a GitHub Actions workflow to build a Maven Java project, use the following example.
## .github/workflows/maven-build.yml
```
name: Maven Build

on:
  push:
    branches:
      - main
      - develop

  pull_request:
    branches:
      - main
      - develop

jobs:
  build:

    runs-on: ubuntu-latest

    steps:

      - name: Checkout Code
        uses: actions/checkout@v4

      - name: Set Up Java
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: '17'

      - name: Cache Maven Dependencies
        uses: actions/cache@v4
        with:
          path: ~/.m2/repository
          key: maven-${{ hashFiles('**/pom.xml') }}
          restore-keys: |
            maven-

      - name: Build with Maven
        run: mvn clean install

      - name: Upload JAR Artifact
        uses: actions/upload-artifact@v4
        with:
          name: application-jar
          path: target/*.jar
```
CI Flow
```
Git Push / Pull Request
         │
         ▼
Checkout Repository
         │
         ▼
Setup Java (JDK 17)
         │
         ▼
Download Maven Dependencies
         │
         ▼
mvn clean install
         │
         ▼
Run Unit Tests
         │
         ▼
Generate JAR/WAR
         │
         ▼
Upload Artifact
```
Build Only (Without Tests)
```
- name: Build Package
  run: mvn clean package -DskipTests
```
Maven Commands Commonly Used
```
mvn clean
mvn compile
mvn test
mvn package
mvn install
mvn verify
```
Difference
```
compile  -> Compiles source code
test     -> Runs unit tests
package  -> Creates JAR/WAR
install  -> Stores artifact in local Maven repo
verify   -> Runs validations/checks
```
Enterprise CI/CD Example
```
Build
  ↓
Unit Test
  ↓
SonarQube Scan
  ↓
Dependency Check
  ↓
Package JAR/WAR
  ↓
Upload Artifact
  ↓
Deploy Dev
  ↓
Approval
  ↓
Deploy QA
  ↓
Approval
  ↓
Deploy Production
```
### Interview Answer

"In GitHub Actions, I create a workflow that triggers on push or pull request events. The workflow checks out the source code, sets up the required JDK using actions/setup-java, caches Maven dependencies, executes mvn clean install to compile and test the application, and then uploads the generated JAR/WAR artifact. This artifact is later used in deployment stages for Dev, QA, and Production environments."

For a Maven project with JaCoCo code coverage in GitHub Actions, use the following workflow.
## .github/workflows/maven-jacoco.yml
```
name: Maven Build with JaCoCo

on:
  push:
    branches:
      - main
      - develop
  pull_request:
    branches:
      - main

jobs:
  build-and-test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Source Code
        uses: actions/checkout@v4

      - name: Set up JDK 17
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: '17'
          cache: maven

      - name: Build, Test and Generate Coverage
        run: mvn clean verify

      - name: Upload JaCoCo Report
        uses: actions/upload-artifact@v4
        with:
          name: jacoco-report
          path: target/site/jacoco/
```
Configure JaCoCo in pom.xml

Add the JaCoCo plugin:
```
<build>
    <plugins>
        <plugin>
            <groupId>org.jacoco</groupId>
            <artifactId>jacoco-maven-plugin</artifactId>
            <version>0.8.11</version>

            <executions>
                <execution>
                    <goals>
                        <goal>prepare-agent</goal>
                    </goals>
                </execution>

                <execution>
                    <id>report</id>
                    <phase>verify</phase>
                    <goals>
                        <goal>report</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```
Enforce Minimum Coverage

Fail the build if coverage drops below 80%:
```
<execution>
    <id>jacoco-check</id>
    <goals>
        <goal>check</goal>
    </goals>
    <configuration>
        <rules>
            <rule>
                <element>BUNDLE</element>
                <limits>
                    <limit>
                        <counter>INSTRUCTION</counter>
                        <value>COVEREDRATIO</value>
                        <minimum>0.80</minimum>
                    </limit>
                </limits>
            </rule>
        </rules>
    </configuration>
</execution>
```
Publish Coverage to GitHub Summary

Add this step after the Maven build:
```
- name: Publish Coverage Summary
  run: |
    echo "## JaCoCo Coverage Report" >> $GITHUB_STEP_SUMMARY
    echo "Coverage report generated successfully." >> $GITHUB_STEP_SUMMARY
```
Maven Commands
```
mvn clean test      # Unit tests
mvn clean verify    # Tests + JaCoCo report
mvn jacoco:report   # Generate report

```
Generated reports:
```
target/
 └── site/
      └── jacoco/
           ├── index.html
           ├── jacoco.xml
           └── jacoco.csv
```
Open target/site/jacoco/index.html to view detailed coverage metrics such as:

Line Coverage
Branch Coverage
Method Coverage
Class Coverage
Package-wise Coverage
Enterprise Pipeline Example
```
Git Push
   ↓
GitHub Actions
   ↓
Maven Clean Verify
   ↓
JaCoCo Coverage Check
   ↓
SonarQube Analysis
   ↓
Build JAR/WAR
   ↓
Publish Artifact
   ↓
Deploy
```
This setup is commonly used in Spring Boot, microservices, and enterprise Java CI/CD pipelines.

To publish a Maven artifact (JAR/WAR) using GitHub Actions, you can upload it as a GitHub Actions artifact or publish it to a Maven repository such as GitHub Packages, Nexus, or Artifactory.
### Option 1: Upload JAR as GitHub Actions Artifact
This is useful for downloading the build output from the workflow run.
```
name: Build and Publish Artifact

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Code
        uses: actions/checkout@v4

      - name: Setup Java
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: '17'
          cache: maven

      - name: Build Application
        run: mvn clean package

      - name: Upload JAR Artifact
        uses: actions/upload-artifact@v4
        with:
          name: my-application
          path: target/*.jar
```
After the workflow completes:
```
GitHub Actions
  → Workflow Run
  → Artifacts
  → Download my-application
```
Option 2: Publish to GitHub Packages
pom.xml
```
<distributionManagement>
    <repository>
        <id>github</id>
        <name>GitHub Packages</name>
        <url>https://maven.pkg.github.com/OWNER/REPOSITORY</url>
    </repository>
</distributionManagement>
```
Replace:
```
OWNER      = GitHub username or organization
REPOSITORY = Repository name
```
GitHub Action
```
name: Publish Package

on:
  push:
    branches:
      - main

jobs:
  publish:
    runs-on: ubuntu-latest

    permissions:
      contents: read
      packages: write

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: '17'

      - name: Publish Package
        run: mvn deploy
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```
Option 3: Publish to Nexus Repository

Configure settings.xml:
```
<servers>
    <server>
        <id>nexus</id>
        <username>${env.NEXUS_USER}</username>
        <password>${env.NEXUS_PASSWORD}</password>
    </server>
</servers>
```
Configure pom.xml:
```
<distributionManagement>
    <repository>
        <id>nexus</id>
        <url>https://nexus.company.com/repository/maven-releases/</url>
    </repository>
</distributionManagement>
```
GitHub Action:
```
- name: Deploy to Nexus
  run: mvn clean deploy
  env:
    NEXUS_USER: ${{ secrets.NEXUS_USER }}
    NEXUS_PASSWORD: ${{ secrets.NEXUS_PASSWORD }}
```
Complete Enterprise Pipeline Example
```
name: Java CI/CD

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: '17'
          cache: maven

      - name: Unit Tests
        run: mvn clean test

      - name: Generate JaCoCo Report
        run: mvn verify

      - name: Package Application
        run: mvn package

      - name: Upload Build Artifact
        uses: actions/upload-artifact@v4
        with:
          name: springboot-jar
          path: target/*.jar

      - name: Deploy to Nexus
        run: mvn deploy
        env:
          NEXUS_USER: ${{ secrets.NEXUS_USER }}
          NEXUS_PASSWORD: ${{ secrets.NEXUS_PASSWORD }}
```
### Common Interview Question

#### Q: What is the difference between upload-artifact and mvn deploy?

upload-artifact: Stores build files within the GitHub Actions workflow for download.
mvn deploy: Publishes artifacts to a Maven repository (GitHub Packages, Nexus, Artifactory) for use by other applications and teams.

In most enterprise environments, the flow is:
```
GitHub Push
   ↓
Build (mvn clean verify)
   ↓
JaCoCo Coverage
   ↓
Upload Artifact
   ↓
Publish to Nexus/Artifactory
   ↓
Deploy to Dev/Test/Prod
```
### How do you secure credentials in GitHub Actions?

1. Store secrets in GitHub Secrets instead of hardcoding them.
2. Use Environment Secrets for Dev, QA, and Prod.
3. Prefer OIDC federation for AWS/Azure/GCP instead of storing access keys.
4. Use the built-in GITHUB_TOKEN where possible.
5. Apply least-privilege permissions to workflows.
6. Enable secret scanning and regularly rotate credentials.
7. Never print secrets in logs or commit them to repositories.


To build a Maven package in Docker and push the Docker image to JFrog Artifactory or a container registry using GitHub Actions, a typical enterprise pipeline looks like:
```
Git Push
   ↓
Maven Build & Test
   ↓
Generate JAR
   ↓
Build Docker Image
   ↓
Login to Registry/JFrog
   ↓
Push Docker Image
   ↓
Deploy
```
Project Structure
```
project/
├── src/
├── pom.xml
├── Dockerfile
└── .github/
    └── workflows/
        └── docker-build.yml
```
Dockerfile
```
FROM eclipse-temurin:17-jre

WORKDIR /app

COPY target/*.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java","-jar","app.jar"]
```
### Option 1: Push Docker Image to JFrog Artifactory
GitHub Secrets

Configure these secrets:
```
JFROG_URL
JFROG_USERNAME
JFROG_PASSWORD
```
Example:
```
mycompany.jfrog.io
```
GitHub Actions Workflow
```
name: Build and Push Docker Image to JFrog

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
          distribution: temurin
          java-version: '17'
          cache: maven

      - name: Build Maven Package
        run: mvn clean package

      - name: Log in to JFrog Docker Registry
        uses: docker/login-action@v3
        with:
          registry: ${{ secrets.JFROG_URL }}
          username: ${{ secrets.JFROG_USERNAME }}
          password: ${{ secrets.JFROG_PASSWORD }}

      - name: Build Docker Image
        run: |
          docker build -t ${{ secrets.JFROG_URL }}/docker-local/springboot-app:${{ github.run_number }} .

      - name: Push Docker Image
        run: |
          docker push ${{ secrets.JFROG_URL }}/docker-local/springboot-app:${{ github.run_number }}
```
### Option 2: Push to Docker Hub
Secrets
```
DOCKER_USERNAME
DOCKER_PASSWORD
```
Workflow:
```
- name: Login to Docker Hub
  uses: docker/login-action@v3
  with:
    username: ${{ secrets.DOCKER_USERNAME }}
    password: ${{ secrets.DOCKER_PASSWORD }}

- name: Build Docker Image
  run: |
    docker build -t ${{ secrets.DOCKER_USERNAME }}/springboot-app:${{ github.run_number }} .

- name: Push Docker Image
  run: |
    docker push ${{ secrets.DOCKER_USERNAME }}/springboot-app:${{ github.run_number }}
```
### Option 3: Push to Azure Container Registry (ACR)

```
- name: Azure Login
  uses: azure/login@v2
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}

- name: Login ACR
  run: |
    az acr login --name myacr

- name: Build Docker Image
  run: |
    docker build -t myacr.azurecr.io/app:${{ github.run_number }} .

- name: Push Image
  run: |
    docker push myacr.azurecr.io/app:${{ github.run_number }}
```
Enterprise Example with JaCoCo + Docker + JFrog
```
name: CI-CD Pipeline

on:
  push:
    branches:
      - main

jobs:
  build-publish:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: '17'
          cache: maven

      - name: Run Unit Tests
        run: mvn clean test

      - name: Generate JaCoCo Coverage
        run: mvn verify

      - name: Build JAR
        run: mvn package

      - name: Upload JAR Artifact
        uses: actions/upload-artifact@v4
        with:
          name: application-jar
          path: target/*.jar

      - name: Login to JFrog
        uses: docker/login-action@v3
        with:
          registry: ${{ secrets.JFROG_URL }}
          username: ${{ secrets.JFROG_USERNAME }}
          password: ${{ secrets.JFROG_PASSWORD }}

      - name: Build Docker Image
        run: |
          docker build \
          -t ${{ secrets.JFROG_URL }}/docker-local/app:${{ github.sha }} .

      - name: Push Docker Image
        run: |
          docker push \
          ${{ secrets.JFROG_URL }}/docker-local/app:${{ github.sha }}
```
### Interview Answer

### Q: How do you build a Maven application, create a Docker image, and push it to JFrog using GitHub Actions?

Answer:

Checkout code using actions/checkout.
Configure Java using actions/setup-java.
Run mvn clean package to generate the JAR.
Build a Docker image using a Dockerfile.
Store JFrog credentials in GitHub Secrets.
Authenticate using docker/login-action.
Tag the image with the build number or Git SHA.
Push the image to JFrog Artifactory using docker push.
Optionally perform security scans and deploy to Kubernetes/OpenShift after the push.

### . GitHub Actions Automatic Rollback

Example Kubernetes workflow:
```
jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Deploy
        run: |
          kubectl apply -f deployment.yaml

      - name: Health Check
        run: |
          kubectl rollout status deployment/myapp --timeout=120s

      - name: Rollback
        if: failure()
        run: |
          kubectl rollout undo deployment/myapp
```
Enterprise GitHub Actions Example
```
name: Deploy Application

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Deploy to Kubernetes
        run: |
          kubectl apply -f deployment.yaml

      - name: Validate Deployment
        run: |
          kubectl rollout status deployment/myapp --timeout=180s

      - name: Rollback Deployment
        if: failure()
        run: |
          kubectl rollout undo deployment/myapp

      - name: Notify Team
        if: failure()
        run: |
          echo "Deployment failed and rollback completed"
```
### Interview Answer

### How do you handle rollback on a failed deployment?

Deploy using versioned artifacts/images (Git SHA or semantic versions).
Perform health checks after deployment.
If validation fails, trigger an automated rollback.
In Kubernetes, use kubectl rollout undo.
For Helm, use helm rollback.
For cloud platforms, use deployment slots, task definition revisions, or previous artifact versions.
Prefer Blue-Green or Canary deployments because they allow fast and safe rollback with minimal downtime.

### How to Automate Rollback in GitHub Actions?

Rollback automation depends on what you're deploying:

Application deployment (VMs, App Services, Kubernetes)
Docker containers
Terraform infrastructure
Azure/AWS resources

The general approach is:
```
Deploy New Version
       │
       ▼
Health Check
       │
   Success?
   ├─ Yes → Keep Release
   └─ No  → Rollback Previous Version
```
Method 1: Rollback Using Job Failure

GitHub Actions provides the failure() condition.
```
jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Deploy Application
        run: ./deploy.sh

      - name: Health Check
        run: ./health-check.sh

  rollback:
    needs: deploy
    if: failure()
    runs-on: ubuntu-latest

    steps:
      - name: Rollback Deployment
        run: ./rollback.sh
```
### Interview Answer

If the deployment or health check fails, GitHub Actions triggers a rollback job using the if: failure() condition.

### Real Enterprise CI/CD Rollback Flow
```
Developer Merge
        │
        ▼
Build
        │
        ▼
Unit Tests
        │
        ▼
Deploy to Staging
        │
        ▼
Smoke Tests
        │
        ▼
Deploy Production
        │
        ▼
Health Check
        │
        ├─ Success
        │      ▼
        │   Complete
        │
        └─ Failure
               ▼
         Automatic Rollback
               ▼
      Previous Stable Release
```
### Interview Answer (5+ Years DevOps)

"In GitHub Actions, I automate rollback by combining deployment validation and failure handling. After deployment, automated smoke tests and health checks run. If a deployment fails or health checks return errors, a rollback job is triggered using if: failure(). For Kubernetes, I use kubectl rollout undo; for Azure App Services, deployment slots and slot swaps; for Docker, redeployment of the last stable image; and for Terraform, rollback is typically achieved by reverting the Git commit and reapplying the previous infrastructure code. This ensures minimal downtime and rapid recovery."

### GitHub Actions Integration with AWS, Azure, and Terraform

In real-world DevOps projects, GitHub Actions acts as the CI/CD orchestrator, Terraform provisions infrastructure, and AWS/Azure host the resources.

End-to-End Flow
```
Developer Commit
      │
      ▼
GitHub Repository
      │
      ▼
GitHub Actions Trigger
      │
      ▼
Authenticate to AWS/Azure
      │
      ▼
Terraform Init
      │
      ▼
Terraform Validate
      │
      ▼
Terraform Plan
      │
      ▼
Approval (Optional)
      │
      ▼
Terraform Apply
      │
      ▼
AWS/Azure Resources Created
```
### Option 1: GitHub Actions + AWS + Terraform
Step 1: Configure AWS Credentials

Store credentials in GitHub Secrets:
```
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
AWS_REGION
```
Step 2: GitHub Actions Workflow
```
name: Terraform AWS

on:
  push:
    branches:
      - main

jobs:
  terraform:

    runs-on: ubuntu-latest

    steps:

      - name: Checkout
        uses: actions/checkout@v4

      - name: Configure AWS Credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: us-east-1

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
AWS Provider
```
provider "aws" {
  region = "us-east-1"
}
```
Example Resource:
```
resource "aws_instance" "web" {
  ami           = "ami-123456"
  instance_type = "t2.micro"
}
```
### Option 2: GitHub Actions + Azure + Terraform
Step 1: Create Service Principal
```
az ad sp create-for-rbac \
  --name github-sp \
  --role Contributor \
  --scopes /subscriptions/<subscription-id>
```
Store output in GitHub Secrets:
```
AZURE_CLIENT_ID
AZURE_CLIENT_SECRET
AZURE_SUBSCRIPTION_ID
AZURE_TENANT_ID
```
Step 2: Azure Login
```
- name: Azure Login
  uses: azure/login@v2
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```
Complete Workflow
```
name: Terraform Azure

on:
  push:
    branches:
      - main

jobs:

  terraform:

    runs-on: ubuntu-latest

    steps:

      - name: Checkout
        uses: actions/checkout@v4

      - name: Azure Login
        uses: azure/login@v2
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      - name: Setup Terraform
        uses: hashicorp/setup-terraform@v3

      - name: Terraform Init
        run: terraform init

      - name: Terraform Plan
        run: terraform plan

      - name: Terraform Apply
        run: terraform apply -auto-approve
```
Azure Provider
```
provider "azurerm" {
  features {}
}
```
Example:
```
resource "azurerm_resource_group" "rg" {
  name     = "rg-dev"
  location = "East US"
}
```
### Best Practice: Use OIDC Instead of Secrets

Avoid storing long-term AWS/Azure credentials.

AWS OIDC
```
permissions:
  id-token: write
  contents: read
```
```
- uses: aws-actions/configure-aws-credentials@v4
  with:
    role-to-assume: arn:aws:iam::123456789012:role/github-role
    aws-region: us-east-1
```
Benefits:

No access keys
More secure
Recommended by AWS

Azure OIDC
```
- name: Azure Login
  uses: azure/login@v2
  with:
    client-id: ${{ secrets.AZURE_CLIENT_ID }}
    tenant-id: ${{ secrets.AZURE_TENANT_ID }}
    subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
```
Benefits:

Passwordless authentication
Short-lived tokens
Enterprise best practice

### Terraform Backend Configuration
AWS S3 Backend
```
terraform {
  backend "s3" {
    bucket         = "terraform-state"
    key            = "prod/terraform.tfstate"
    region         = "us-east-1"
    dynamodb_table = "terraform-locks"
  }
}
```
Azure Storage Backend
```
terraform {
  backend "azurerm" {
    resource_group_name  = "rg-tfstate"
    storage_account_name = "tfstateprod"
    container_name       = "tfstate"
    key                  = "prod.tfstate"
  }
}
```
Enterprise CI/CD Flow
```
Developer Pushes Code
        │
        ▼
GitHub Actions
        │
        ▼
Terraform fmt
        │
        ▼
Terraform validate
        │
        ▼
Security Scan (Checkov/TFSec)
        │
        ▼
Terraform Plan
        │
        ▼
Manager Approval
        │
        ▼
Terraform Apply
        │
        ▼
AWS/Azure Deployment
        │
        ▼
Smoke Test
        │
```
### How have you integrated GitHub Actions with Terraform and Azure/AWS?

Answer:

"I use GitHub Actions as the CI/CD platform and Terraform as Infrastructure as Code. The workflow is triggered by pull requests or merges to the main branch. GitHub Actions authenticates with AWS using IAM roles/OIDC or with Azure using a Service Principal/OIDC. The pipeline executes terraform init, validate, plan, and apply. Terraform state is stored remotely in S3 with DynamoDB locking for AWS or Azure Storage Account for Azure. Before production deployment, approval gates are configured using GitHub Environments, and security scans such as Checkov or TFSec are included in the pipeline."

## A typical enterprise-grade GitHub Actions CI/CD pipeline for a Java Maven application looks like this:

```
Developer Push
      │
      ▼
GitHub Actions Trigger
      │
      ▼
Checkout Code
      │
      ▼
Maven Build
      │
      ▼
Unit Tests
      │
      ▼
JaCoCo Coverage
      │
      ▼
SonarQube Analysis
      │
      ▼
Quality Gate Validation
      │
      ├── Failed → Stop Pipeline
      │
      ▼
Build JAR
      │
      ▼
Publish Artifact to JFrog Artifactory
      │
      ▼
Build Docker Image
      │
      ▼
Security Scan (Trivy/Snyk)
      │
      ▼
Push Docker Image to JFrog Container Registry
      │
      ▼
Deploy DEV
      │
      ▼
Smoke Tests
      │
      ▼
Approval
      │
      ▼
Deploy QA
      │
      ▼
Regression Tests
      │
      ▼
Approval
      │
      ▼
Deploy UAT
      │
      ▼
Business Validation
      │
      ▼
Approval
      │
      ▼
Deploy PROD
      │
      ▼
Health Checks
      │
      ├── Failed → Rollback
      │
      ▼
Success
```
GitHub Actions Workflow
```
name: Enterprise CI-CD

on:
  push:
    branches:
      - main

env:
  IMAGE_NAME: springboot-app

jobs:

##################################################
# BUILD & TEST
##################################################
  build:
    runs-on: ubuntu-latest

    outputs:
      image_tag: ${{ github.sha }}

    steps:

      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Java
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: '17'
          cache: maven

      - name: Build
        run: mvn clean compile

      - name: Unit Tests
        run: mvn test

      - name: JaCoCo Coverage
        run: mvn verify

##################################################
# SONARQUBE
##################################################
      - name: SonarQube Scan
        run: |
          mvn sonar:sonar \
          -Dsonar.host.url=${{ secrets.SONAR_HOST_URL }} \
          -Dsonar.login=${{ secrets.SONAR_TOKEN }}

      - name: Sonar Quality Gate
        uses: sonarsource/sonarqube-quality-gate-action@master
        timeout-minutes: 5
        env:
          SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}

##################################################
# PACKAGE & PUBLISH TO JFROG
##################################################
      - name: Package
        run: mvn package

      - name: Publish Artifact to JFrog
        run: mvn deploy
        env:
          JFROG_USER: ${{ secrets.JFROG_USER }}
          JFROG_PASSWORD: ${{ secrets.JFROG_PASSWORD }}

##################################################
# BUILD DOCKER IMAGE
##################################################
      - name: Docker Login
        uses: docker/login-action@v3
        with:
          registry: ${{ secrets.JFROG_REGISTRY }}
          username: ${{ secrets.JFROG_USER }}
          password: ${{ secrets.JFROG_PASSWORD }}

      - name: Build Docker Image
        run: |
          docker build \
          -t ${{ secrets.JFROG_REGISTRY }}/docker-local/${IMAGE_NAME}:${{ github.sha }} .

##################################################
# SECURITY SCAN
##################################################
      - name: Scan Docker Image
        uses: aquasecurity/trivy-action@master
        with:
          image-ref: ${{ secrets.JFROG_REGISTRY }}/docker-local/${IMAGE_NAME}:${{ github.sha }}

##################################################
# PUSH IMAGE
##################################################
      - name: Push Image
        run: |
          docker push \
          ${{ secrets.JFROG_REGISTRY }}/docker-local/${IMAGE_NAME}:${{ github.sha }}

##################################################
# DEPLOY DEV
##################################################
  deploy-dev:
    needs: build
    runs-on: ubuntu-latest
    environment: dev

    steps:

      - name: Deploy DEV
        run: |
          helm upgrade --install app helm-chart \
          --namespace dev \
          --set image.tag=${{ github.sha }}

      - name: Health Check
        run: |
          kubectl rollout status deployment/app -n dev

##################################################
# DEPLOY QA
##################################################
  deploy-qa:
    needs: deploy-dev
    runs-on: ubuntu-latest
    environment: qa

    steps:

      - name: Deploy QA
        run: |
          helm upgrade --install app helm-chart \
          --namespace qa \
          --set image.tag=${{ github.sha }}

      - name: Run Regression Tests
        run: |
          echo "Executing regression suite"

##################################################
# DEPLOY UAT
##################################################
  deploy-uat:
    needs: deploy-qa
    runs-on: ubuntu-latest
    environment: uat

    steps:

      - name: Deploy UAT
        run: |
          helm upgrade --install app helm-chart \
          --namespace uat \
          --set image.tag=${{ github.sha }}

##################################################
# DEPLOY PROD
##################################################
  deploy-prod:
    needs: deploy-uat
    runs-on: ubuntu-latest
    environment: production

    steps:

      - name: Deploy PROD
        run: |
          helm upgrade --install app helm-chart \
          --namespace prod \
          --set image.tag=${{ github.sha }}

      - name: Validate Deployment
        run: |
          kubectl rollout status deployment/app -n prod

##################################################
# AUTO ROLLBACK
##################################################
      - name: Rollback
        if: failure()
        run: |
          kubectl rollout undo deployment/app -n prod
```
Required GitHub Secrets
```
SONAR_HOST_URL
SONAR_TOKEN

JFROG_USER
JFROG_PASSWORD
JFROG_REGISTRY

KUBE_CONFIG_DEV
KUBE_CONFIG_QA
KUBE_CONFIG_UAT
KUBE_CONFIG_PROD
```
Environment Protection Rules

Configure in GitHub:
```
Settings
  └─ Environments

      DEV
      QA
      UAT
      PROD
```

For QA/UAT/PROD:
```
Required Reviewers:
  Dev Lead
  QA Lead
  Release Manager
```
This creates a promotion flow:
```
DEV     → Auto
QA      → Approval
UAT     → Approval
PROD    → Approval
```
Production Best Practices
Image Tagging

Use immutable tags:
```
app:1.0.0
app:1.0.1
app:${GITHUB_SHA}
```
Security Gates

Before deployment:
```
SonarQube Pass
JaCoCo ≥ 80%
Trivy Scan Pass
Artifact Published
Docker Push Success
```
### Q. 

Code is pushed to GitHub, GitHub Actions triggers the pipeline, Maven builds and tests the application, JaCoCo generates coverage, SonarQube validates code quality, the JAR is published to JFrog Artifactory, a Docker image is built and scanned, pushed to JFrog Container Registry, then deployed sequentially to DEV, QA, UAT, and PROD Kubernetes environments using Helm with approval gates between environments. Health checks validate deployments, and automatic rollback is performed if production deployment fails.
