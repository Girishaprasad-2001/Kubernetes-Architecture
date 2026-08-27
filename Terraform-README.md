## Terraform A to Z Commands with Explanation and Real-Time Examples

Terraform is an Infrastructure as Code (IaC) tool used to provision and manage cloud resources.

1. Check Terraform Version
```
 terraform version
```

Output:

```
Terraform v1.9.x
```

Used to verify the installed version.

2. Initialize Terraform
``
terraform init
```

Purpose:

Downloads provider plugins.
Creates .terraform directory.
Initializes backend.

Example:

```
terraform init
```

Output:

```
Initializing provider plugins...
2
Terraform has been successfully initialized!
```
3. Validate Configuration
```
terraform validate
```

Checks:

Syntax errors
Invalid arguments
Configuration issues

Example:
```
terraform validate
```

Output:

```
Success! The configuration is valid.
```
4. Format Terraform Files
```
terraform fmt
```

Formats .tf files.

Before:

Terraform
```
1
resource "aws_instance" "web"{
2
ami="ami-123"
3
}
```

After:

Terraform
```
1
resource "aws_instance" "web" {
2
ami = "ami-123"
3
}
```
5. Generate Execution Plan
```
terraform plan
```

Shows:

Resources to create
Resources to update
Resources to destroy

Example:

```
terraform plan
```

Output:

```
+ aws_instance.web will be created
```

No changes are made.

6. Apply Infrastructure
```
terraform apply
```

Creates resources.

Example:

```
terraform apply
```

Auto-approve:

```
terraform apply -auto-approve
```

Output:

```
Apply complete!
```
7. Destroy Infrastructure
```
terraform destroy
```

Deletes managed resources.

Example:

```
terraform destroy
```

Skip confirmation:

```
terraform destroy -auto-approve
```
8. Show Current State
```
terraform show
```

Displays:

Current state
Resource attributes

Example:

```
terraform show
```
9. Show State Resources
```
terraform state list
```

Output:

```
aws_instance.web
2
aws_security_group.sg
```
10. View Resource Details
```
terraform state show aws_instance.web
```

Displays detailed attributes.

11. Remove Resource from State
```
terraform state rm aws_instance.web
```

Removes from Terraform state only.

Resource remains in cloud.

Real-world use:

```
Terraform state corruption recovery
```
12. Move State
```
terraform state mv
```

Example:

```
terraform state mv aws_instance.old aws_instance.new
```

Useful during refactoring.

13. Import Existing Resource

Suppose EC2 already exists.

```
terraform import aws_instance.web i-123456789
```

Now Terraform manages it.

14. Refresh State

Older command:

```
terraform refresh
```

Updates state with actual infrastructure values.

(Note: Modern versions perform refresh during plan/apply.)

15. Output Variables
```
terraform output
```

Example:

Terraform
```
1
output "instance_ip" {
2
value = aws_instance.web.public_ip
3
}
```

Command:

```
terraform output
```
Output:

```
instance_ip = 54.x.x.x
```

Specific output:

```
terraform output instance_ip
```
16. Terraform Console
```
terraform console
```

Interactive console.

Example:

```
> 5+5
2
10
```
```
> upper("azure")
2
AZURE
```
17. Terraform Providers

Show providers:

```
terraform providers
```

Output:

```
provider[registry.terraform.io/hashicorp/aws]
```
18. Graph Dependency Tree
```
terraform graph
```

Displays resource dependency graph.

Used for troubleshooting dependencies.

19. Lock Providers
```
terraform providers lock
```

Creates lock file.

```
.terraform.lock.hcl
```

Ensures consistent provider versions.

20. Workspace Commands
Create Workspace
```
terraform workspace new dev
```
List Workspaces
```
terraform workspace list
```

Output:

```
* default
2
dev
3
prod
```
Switch Workspace
```
terraform workspace select prod
```
Show Current Workspace
```
terraform workspace show
```
Delete Workspace
```
terraform workspace delete dev
```
21. State Pull

Download remote state.

```
terraform state pull
```

Used with remote backends.

22. State Push

Upload state manually.

```
terraform state push terraform.tfstate
```

Rarely used.

23. Force Unlock

If state file is locked:

```
terraform force-unlock LOCK_ID
```

Example:

```
terraform force-unlock abc123xyz
```
24. Taint Resource

Mark for recreation.

```
terraform taint aws_instance.web
```

Next apply recreates resource.

25. Untaint Resource
```
terraform untaint aws_instance.web
```

Removes taint.

26. Target Specific Resource
```
terraform apply -target=aws_instance.web
```

Only applies one resource.

27. Create Plan File
```
terraform plan -out=tfplan
```

Apply later:

```
terraform apply tfplan
```

Common in CI/CD.

28. Backend Migration
```
terraform init -migrate-state
```

Used when migrating state:

```
Local → S3
2
S3 → Azure Blob Storage
```
29. Upgrade Providers
```
terraform init -upgrade
```

Downloads latest compatible provider versions.

30. List Resources in State
```
terraform state list
```

Example:

```
aws_vpc.main
2
aws_instance.web
3
aws_s3_bucket.logs
```
Real-Time AWS Example
main.tf
Terraform
1
provider "aws" {
2
region = "us-east-1"
3
}
4
 
5
resource "aws_instance" "web" {
6
ami = "ami-123456"
7
instance_type = "t2.micro"
8
}
```
Initialize
```
terraform init
```
Validate
```
terraform validate
```
Plan
```
terraform plan
```
Create
```
terraform apply
```
Verify
```
terraform state list
```
Show Details
```
terraform show
```
Destroy
```
terraform destroy
```
Most Important Interview Commands
```
terraform init
2
terraform validate
3
terraform fmt
4
terraform plan
5
terraform apply
6
terraform destroy
7
terraform show
8
terraform output
9
terraform import
10
terraform state list
11
terraform state show
12
terraform workspace list
13
terraform workspace select
14
terraform force-unlock
15
terraform providers
```
### 2-Minute Interview Answer

Terraform commands follow a typical workflow. terraform init initializes the project and downloads providers. terraform validate checks syntax, terraform fmt formats code, and terraform plan shows the changes Terraform will make. terraform apply provisions infrastructure, while terraform destroy removes it. State management commands such as terraform state list, terraform state show, and terraform import help manage existing resources. Workspaces are used to separate environments like dev, test, and prod. These commands form the core Terraform lifecycle used in real-world DevOps and cloud automation projects.


# Terraform Import, Data Source, Module, and Taint - Complete Explanation

These are common Terraform interview topics and are often asked together.

1. Terraform Import
What is Terraform Import?

terraform import is used to bring an existing resource into Terraform state without recreating it.

Scenario

An EC2 instance already exists in AWS.

```
AWS EC2 Instance
2
↓
3
Created Manually
4
↓
5
Terraform doesn't know about it
```

Use import:

```
terraform import aws_instance.web i-0123456789abcdef0
```

Now Terraform manages it.

Example
Resource Block
Terraform
```
1
resource "aws_instance" "web" {
2
}
```
Import
```
terraform import aws_instance.web i-0123456789abcdef0
```
Verify
```
terraform state list
```
```
terraform state show aws_instance.web
```
Workflow
```
Existing Resource
2
↓
3
terraform import
4
↓
5
Terraform State
6
↓
7
Managed by Terraform
```
Important Points
Resource already exists.
Terraform adds it to state.
Does NOT generate complete Terraform code.
After import, update configuration manually.
Import Block (Terraform 1.5+)

Instead of CLI:

Terraform
```
1
import {
2
to = aws_instance.web
3
id = "i-0123456789abcdef0"
4
}
```

Run:

```
terraform apply
```
# Interview Answer

Terraform Import is used to bring existing infrastructure under Terraform management by associating a real-world resource with a Terraform resource block and storing that mapping in the state file.

## 2. Terraform Data Source
What is a Data Source?

A data source allows Terraform to read information about existing resources without managing them.

Syntax
Terraform
```
1
data "<provider>_<resource>" "<name>" {
2
}
```
Example: Existing VPC
Terraform
```
1
data "aws_vpc" "prod" {
2
id = "vpc-123456"
3
}
```

Use it:

Terraform
```
1
resource "aws_subnet" "subnet1" {
2
vpc_id = data.aws_vpc.prod.id
3
cidr_block = "10.0.1.0/24"
4
}
```
Workflow
```
Existing VPC
2
↓
3
Data Source Reads It
4
↓
5
Terraform Uses Attributes
```
Characteristics

✅ Read-only

✅ No resource creation

✅ No deletion

✅ No lifecycle management

Data Source vs Import
Data Source
Terraform
```
1
data "aws_vpc" "prod" {
2
id = "vpc-123456"
3
}
```

Purpose:

```
Read Existing Resource
```

Terraform can't destroy it.

Import
Terraform
```
1
resource "aws_vpc" "prod" {
2
}
3
 
```
```
terraform import aws_vpc.prod vpc-123456
```

Purpose:

```
Manage Existing Resource
2
``
```

Terraform can modify or destroy it.

Comparison
Feature	Data Source	ImportCreate Resource	No	No
Read Resource	Yes	Yes
Manage Lifecycle	No	Yes
Stored in State	Temporary Read	Managed Resource
Destroy Possible	No	Yes

## 3. Terraform Module
What is a Module?

A module is a reusable collection of Terraform resources.

Without Module
```
VPC.tf
2
Subnet.tf
3
EC2.tf
4
 
5
Repeated Again
6
Repeated Again
7
Repeated Again
```

Lots of duplication.

With Module
```
Module Created Once
2
↓
3
Reuse Multiple Times
```
Root Module
```
main.tf
```

Terraform execution starts here.

Child Module Structure
```
modules/
2
└── ec2/
3
├── main.tf
4
├── variables.tf
5
└── outputs.tf
```
Child Module Example
main.tf
Terraform
```
1
resource "aws_instance" "server" {
2
ami = var.ami
3
instance_type = var.instance_type
4
}
```
variables.tf
Terraform
```
1
variable "ami" {}
2
 
3
variable "instance_type" {}
```
outputs.tf
Terraform
```
1
output "instance_id" {
2
value = aws_instance.server.id
3
}
```
Call Module
Root Module
Terraform
```
1
module "webserver" {
2
source = "./modules/ec2"
3
 
4
ami = "ami-0abc1234"
5
instance_type = "t2.micro"
6
}
```

Apply:

```
terraform apply
```
Module Workflow
```
Root Module
2
↓
3
Calls Child Module
4
↓
5
Creates Resources
```
Types of Modules
Local Module
Terraform
```
1
source = "./modules/ec2"
```
Git Module
Terraform
```
1
source = "git::https://github.com/org/modules.git//ec2"
```
Registry Module
Terraform
```
1
source = "terraform-aws-modules/vpc/aws"
```
Benefits
Reusability
Standardization
Easier maintenance
Scalability
## 4. Terraform Taint
What is Taint?

Taint marks a resource as damaged or requiring recreation.

Next Apply:

```
Destroy Old Resource
2
↓
3
Create New Resource
```
Example

EC2 state:
```
aws_instance.web
```

Mark resource:

```
terraform taint aws_instance.web
```

Output:

```
Resource marked as tainted
```

Run:

```
terraform apply
```
Terraform:

```
Destroy EC2
2
Create New EC2
3
 
```
Workflow
```
Resource Problem
2
↓
3
terraform taint
4
↓
5
Marked for Recreation
6
↓
7
terraform apply
8
↓
9
New Resource Created
```
Untaint

Remove taint:

```
terraform untaint aws_instance.web
```
Modern Replacement (Terraform 0.15+)

HashiCorp recommends:

```
terraform apply -replace="aws_instance.web"
2

```

Example:

```
terraform apply -replace="aws_instance.web"
```

Terraform Plan:

```
-/+ aws_instance.web
```

Meaning:

```
Destroy
2
Create
3
 
```
Taint vs Replace
Old Method
```
terraform taint aws_instance.web
2
terraform apply
3
 
```

Two-step process.

New Method
```
terraform apply -replace="aws_instance.web"
```

One-step process.

Preferred in newer Terraform versions.

Real-Time Example

Suppose:

```
AWS EC2
```

Problems:

Corrupted OS
Wrong software installation
Provisioner failed

Instead of manually deleting:

```
terraform apply -replace="aws_instance.web"
```

Terraform:

```
Delete Old EC2
2
Create New EC2
3
Update State
```

Automatically.

# Interview Summary
## Terraform Import

Used to bring existing resources under Terraform management by adding them to the Terraform state.

## Data Source

Used to read information about existing infrastructure without managing it.

## Module

A reusable collection of Terraform configurations used to standardize and simplify infrastructure deployment.

## Taint / Replace

Used to force Terraform to recreate a resource. terraform taint is the older method, while terraform apply -replace is the recommended modern approach.

## 1. Difference between count and for_each
count

Creates multiple resource instances based on a number.

Terraform
```
1
resource "aws_instance" "server" {
2
count = 3
3
}
4
 
```

Access:

Terraform
```
1
aws_instance.server[0]
```
for_each

Creates resources using unique keys from a map or set.

Terraform
```
1
resource "aws_s3_bucket" "bucket" {
2
for_each = toset(["dev", "qa", "prod"])
3
}
```

Access:

Terraform
```
1
aws_s3_bucket.bucket["dev"]
```
## Interview Answer
count uses numeric indexes (0,1,2...)
for_each uses unique keys
If one resource is removed in count, indexes shift and may cause unnecessary recreation.
for_each maintains stable resource identities and is preferred for production use.
## 2. How does Terraform state work?

Terraform State (terraform.tfstate) stores mappings between Terraform configuration and real infrastructure.

Example:

```
EC2 Instance
2
↔
3
terraform.tfstate
```

Terraform uses state to:

Track existing resources
Detect configuration drift
Generate execution plans
Avoid recreating resources unnecessarily
### Interview Answer

"Terraform state is a JSON file that stores infrastructure metadata and resource mappings. Terraform compares configuration with the state file and actual infrastructure to determine what changes need to be applied."

### 3. What is State Locking?

State locking prevents multiple users from modifying the same state file simultaneously.

Without locking:

```
User A → Apply
2
User B → Apply
```

Result:

State corruption
Resource conflicts

With locking:

```
User A -> Gets Lock
2
User B -> Waits
```
Common Locking Mechanisms
AWS: DynamoDB
Azure: Storage Account Lease
Terraform Cloud: Built-in Locking
### Interview Answer

"State locking ensures only one Terraform operation can modify the state file at a time, preventing race conditions and state corruption."

### 4. What happens if the state file is deleted?

Terraform loses tracking of resources.

Consequences:

Resources still exist in cloud
Terraform believes resources don't exist
Next terraform apply may attempt to recreate resources

Recovery options:

Restore backup state
Recover remote state
Re-import resources
```
terraform import
```
## Interview Answer

"If the state file is deleted, Terraform loses knowledge of existing resources. Infrastructure remains intact, but Terraform may try to recreate resources unless the state is restored or resources are imported again."

### 5. Explain Remote Backend

Remote backend stores Terraform state remotely instead of locally.

Example:

Terraform
```
1
terraform {
2
backend "s3" {
3
bucket = "tf-state-bucket"
4
key = "prod/state.tfstate"
5
region = "us-east-1"
6
}
7
}
```

Benefits:

Team collaboration
State locking
Versioning
Disaster recovery
Common Backends
S3
Azure Storage
GCS
Terraform Cloud
### Interview Answer

"A remote backend stores Terraform state in a centralized location, enabling collaboration, locking, version control, and higher availability."

## 6. What are Provider Aliases?

Provider aliases allow multiple configurations of the same provider.

Example:

Terraform
```
1
provider "aws" {
2
region = "us-east-1"
3
}
4
 
5
provider "aws" {
6
alias = "west"
7
region = "us-west-2"
8
}
```

Usage:

Terraform
```
1
resource "aws_s3_bucket" "west_bucket" {
2
provider = aws.west
3
}
```
### Interview Answer

"Provider aliases allow Terraform to interact with multiple AWS regions or accounts from the same configuration."

7. Difference between Data Source and Resource
Resource

Creates or manages infrastructure.

Terraform
```
1
resource "aws_instance" "server" {
2
}
```
Data Source

Reads existing infrastructure.

Terraform
```
1
data "aws_vpc" "default" {
2
default = true
3
}
```
### Interview Answer
Resource	Data SourceCreates/Updates infrastructure	Reads infrastructure
Managed by Terraform	Not managed
Can modify cloud resources	Read-only
8. Explain Lifecycle Block

Controls Terraform resource behavior.

Example:

Terraform
```
1
lifecycle {
2
create_before_destroy = true
3
prevent_destroy = true
4
ignore_changes
```
Important Options
create_before_destroy

Creates new resource before deleting old one.

prevent_destroy

Blocks accidental deletion.

ignore_changes

Ignores specified changes.

replace_triggered_by

Forces recreation based on another object's changes.

### Interview Answer

"The lifecycle block customizes how Terraform handles resource creation, updates, and deletion to reduce downtime and prevent accidental destruction."

### 9. How do you handle multiple environments?
Method 1: Workspaces
```
terraform workspace new dev
2
terraform workspace new qa
3
terraform workspace new prod
```
Method 2: Separate Folders
```
environments/
2
├─ dev
3
├─ qa
4
└─ prod
```
Method 3: Terragrunt (Enterprise Preferred)

Shared modules with environment-specific variables.

### Interview Answer

"I typically use reusable modules with separate state files per environment. In enterprise environments, Terragrunt or environment-specific directories are commonly used."

### 10. What is Terragrunt and Why Use It?

Terragrunt is a wrapper around Terraform.

Benefits:

DRY principle
Centralized backend config
Dependency management
Environment management

Example:

Terraform
```
1
include {
2
path = find_in_parent_folders()
3
}
```
## Interview Answer

"Terragrunt reduces code duplication by centralizing backend, provider, and module configurations, making multi-environment Terraform deployments easier."

## 11. How do you import existing resources?

Import existing infrastructure into Terraform state.

Example:

```
terraform import aws_instance.web i-123456789
```

Steps:

Create resource block
Run import
Verify state
```
terraform state list
```
Run plan
```
terraform plan
```
### Interview Answer

"Terraform import allows existing cloud resources to be brought under Terraform management without recreating them."

### 12. How do you secure Terraform secrets?
Don't Store Secrets In

❌ Terraform code

❌ Git repositories

❌ Local variables

Use Secret Managers
Azure Key Vault
AWS Secrets Manager
HashiCorp Vault
Google Secret Manager

Example:

Terraform
```
1
data "aws_secretsmanager_secret_version" "db" {
2
secret_id = "db-password"
3
}
```
Sensitive Variables
Terraform
```
1
variable "password" {
2
sensitive = true
3
}
```
### Interview Answer

"I use secret management tools like Azure Key Vault, Vault, or Secrets Manager and mark Terraform variables as sensitive. Secrets should never be hardcoded or committed to source control."

### 13. What are Dynamic Blocks?

Dynamic blocks generate nested configurations dynamically.

Example:

Terraform
```
1
dynamic "ingress" {
2
for_each = var.rules
3
 
4
content {
5
from_port = ingress.value.port
6
to_port = ingress.value.port
7
protocol = "tcp"
8
}
9
}
```
Use Cases
Security Groups
Load Balancer Rules
Route Tables
## Interview Answer

"Dynamic blocks help create repeated nested configurations programmatically and reduce code duplication."

### 14. Explain Dependency Graph

Terraform builds a dependency graph before execution.

Example:

```
VPC
2
↓
3
Subnet
4
↓
5
EC2
```

Terraform determines:

Creation order
Deletion order
Parallel execution opportunities

Visualize:

```
terraform graph
```
### Interview Answer

"Terraform creates a dependency graph using resource references and dependencies. This graph determines the correct execution order and allows safe parallel operations."

### 15. How does Terraform Cloud differ from Open-Source Terraform?
Open Source Terraform
Local execution
Local/remote state
Community support
No built-in governance
Terraform Cloud
Remote execution
State management
Team collaboration
RBAC
Policy as Code (Sentinel)
Cost estimation
### Interview Answer

"Terraform Cloud extends open-source Terraform with collaboration, remote runs, governance, RBAC, state management, policy enforcement, and cost analysis capabilities."

Bonus Question: What are the Terraform commands used daily?
```
terraform init
2
terraform fmt
3
terraform validate
4
terraform plan
5
terraform apply
6
terraform destroy
7
terraform show
8
terraform output
9
terraform state list
10
terraform state show
11
terraform import
12
terraform workspace list
```

### One-line interview summary:
 "Terraform manages infrastructure through configuration files and state management. Advanced concepts include remote state, state locking, modules, provider aliases, dynamic blocks, lifecycle rules, Terragrunt, and Terraform Cloud for enterprise-scale deployments."

### Interview Answer (Short)

1. Terraform Import is used to bring an existing resource into Terraform state so Terraform can manage its lifecycle.

2. Data Source is used to read information about existing resources and consume their attributes without bringing them under Terraform management.

Example:

Existing EC2 → Want Terraform to manage it → Import
Existing VPC → Just need VPC ID to create subnet → Data Source
## 1. Terraform Import

Purpose: Bring an existing resource under Terraform management.

Scenario

Suppose an EC2 instance was created manually from the AWS Console.

## Important

Import only updates the Terraform state file.

It does not automatically generate Terraform code.

After importing, your .tf files must match the actual resource configuration.
## 2. Data Source

Purpose: Read information about existing resources without managing them.

### Types of Terraform Modules
1. Root Module
```
Current working directory
```
2. Local Module
Terraform
```
1
source = "./modules/ec2"
2
```

4. Git Module
Terraform
```
1
source = "git::https://github.com/company/modules.git//ec2"
```
6. Registry Module
Terraform
```
1
source = "terraform-aws-modules/vpc/aws"
```
### Interview Answer

The Terraform Root Module is the set of Terraform configuration files in the current working directory where Terraform commands are executed. It is automatically loaded by Terraform and can call one or more child modules. Child modules are reusable components that help organize and standardize infrastructure code. If a variable inside a module has a default value, Terraform uses that value when no explicit value is provided.



## 4. Terraform Import: 
Terraform Import allows you to bring existing cloud resources (that weren’t created with Terraform) under Terraform’s management — without recreating them.
### After Import
1. Terraform updates the state file to include the imported resource.
2. You’ll need to write the corresponding resource block in your .tf file so Terraform knows how to manage it.

### Q. i have create 5 instances in terraform, some one unfortunates deleted one instances in ui how to fix this issue in terraform 

This creates drift between Terraform State and Actual Infrastructure.

If someone manually deletes one of the Terraform-managed instances from the AWS Console, the infrastructure drifts from the Terraform state. When I run terraform plan, Terraform detects that the resource is missing and shows it will be recreated. Running terraform apply restores the infrastructure to the desired state defined in the code. This is one of the key benefits of Terraform's declarative model: it continuously tries to make the actual state match the desired state.

### Fix #1 (Recommended)
```
terraform apply
```
Terraform will to reach the desired count of 5. Terraform automatically recreates the missing instance.

### Fix #2: Refresh and Apply
```
terraform plan -refresh-only or terraform refresh
terraform apply
```
Terraform recreates the missing resource.

