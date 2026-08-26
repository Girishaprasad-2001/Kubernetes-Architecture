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
