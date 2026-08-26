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
