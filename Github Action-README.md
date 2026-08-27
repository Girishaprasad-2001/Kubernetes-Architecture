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
