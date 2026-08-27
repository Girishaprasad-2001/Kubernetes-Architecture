### 1. Ansible Architecture
```
Control Node
     |
     | SSH
     ↓
Managed Nodes
(Server1, Server2, Server3)

Components:
```
Inventory
Playbooks
Roles
Modules
Variables
Vault
Facts
```
2. Install Ansible

RHEL/CentOS

```
sudo yum install ansible -y
```

Ubuntu

```
sudo apt update
2
sudo apt install ansible -y
```

Verify:

```
ansible --version
```

Output:

```
ansible [core 2.x.x]
```
3. Inventory Commands

Inventory File
```
[web]
192.168.1.10
192.168.1.11

[db]
192.168.1.20
```

List inventory:

```
ansible-inventory --list
```

Graph inventory:

```
ansible-inventory --graph
```

Output:

```
@all
2
|--@web
3
| |--192.168.1.10
4
| |--192.168.1.11
5
|--@db
6
|--192.168.1.20
```
4. Verify Connectivity

Ping all servers:

```
ansible all -m ping
```

Ping web servers:

```
ansible web -m ping
```

Output:

JSON
1
{
2
"ping": "pong"
3
}
```
5. Ad Hoc Commands
Check Uptime
```
ansible all -a "uptime"
```
Check Memory
```
ansible all -a "free -m"
```
Check Disk Usage
```
ansible all -a "df -h"
```
Create Directory
```
ansible all -m file -a "path=/opt/app state=directory"
```
Create User
```
ansible all -m user -a "name=devops"
```
Delete User
```
ansible all -m user -a "name=devops state=absent"
```
6. Package Management

Install package:

```
ansible all -m yum -a "name=httpd state=present"
```

Ubuntu:

```
ansible all -m apt -a "name=apache2 state=present"
```

Latest version:

```
ansible all -m yum -a "name=httpd state=latest"
```

Remove package:

```
ansible all -m yum -a "name=httpd state=absent"
```
7. Service Management

Start service:

```
ansible all -m service -a "name=httpd state=started"
```

Stop service:

```
ansible all -m service -a "name=httpd state=stopped"
```

Restart service:

```
ansible all -m service -a "name=httpd state=restarted"
```

Enable at boot:

```
ansible all -m service -a "name=httpd enabled=yes"
```
8. File Operations

Copy file:

```
ansible all -m copy -a "src=test.txt dest=/tmp/test.txt"
```

Fetch file:

```
ansible all -m fetch -a "src=/tmp/test.txt dest=/backup/"
```

Remove file:

```
ansible all -m file -a "path=/tmp/test.txt state=absent"
```

Change permissions:

```
ansible all -m file -a "path=/tmp/test.txt mode=0755"
```
9. Command and Shell Modules

Command Module

```
ansible all -m command -a "hostname"
```

Shell Module

```
ansible all -m shell -a "ps -ef | grep java"
```

Difference:

```
command → No shell operators
2
shell → Supports pipes, redirects, variables
```
10. Ansible Playbooks

Example:
```
---
- name: Install Apache
  hosts: web

  tasks:
    - name: Install httpd
      yum:
        name: httpd
        state: present

    - name: Start service
      service:
        name: httpd
        state: started
```

Run playbook:

```
ansible-playbook apache.yml
```
11. Check Mode (Dry Run)

Validate changes before execution.

```
ansible-playbook apache.yml --check
```
12. Syntax Check
```
ansible-playbook apache.yml --syntax-check
```
13. List Tasks
```
ansible-playbook apache.yml --list-tasks
```
14. List Hosts
```
ansible-playbook apache.yml --list-hosts
```
15. Variables

Variable File

```
port: 8080
```

Usage

```
- debug:
2
msg: "{{ port }}"
```

Pass variable:

```
ansible-playbook app.yml -e "port=9090"
```
16. Facts Gathering

Gather facts:

```
ansible all -m setup
```

Filter facts:

```
ansible all -m setup -a "filter=ansible_hostname"
```

Example output:

```
ansible_hostname: server1
```
17. Tags

Playbook:

```
tasks:
2
- name: Install Package
3
tags:
4
- install
```

Run specific tag:

```
ansible-playbook app.yml --tags install
```

Skip tag:

```
ansible-playbook app.yml --skip-tags install
```
18. Roles

Role creation:

```
ansible-galaxy init apache
```

Structure:

```
apache/
2
├── tasks
3
├── handlers
4
├── vars
5
├── defaults
6
├── templates
7
├── files
8
└── meta
9

```

Run role:

YAML
```
1
roles:
2
- apache
```
19. Handlers

Triggered only when changes occur.

YAML
```
1
tasks:
2
- name: Update Config
3
template:
4
src: httpd.conf.j2
5
dest: /etc/httpd/conf/httpd.conf
6
notify:
7
- restart apache
8
 
9
handlers:
10
- name: restart apache
11
service:
12
name: httpd
13
state: restarted
```
20. Templates (Jinja2)

Template file:
```
Jinja
1
ServerName {{ hostname }}
```

Task:

YAML
```
1
template:
2
src: httpd.conf.j2
3
dest: /etc/httpd/conf/httpd.conf
```
21. Ansible Vault

Encrypt file:

```
ansible-vault create secrets.yml
```

Edit:

```
ansible-vault edit secrets.yml
```

View:

```
ansible-vault view secrets.yml
```

Encrypt existing file:

```

ansible-vault encrypt secrets.yml
```

Decrypt:

```
ansible-vault decrypt secrets.yml
2
 
```

Run playbook:

```
ansible-playbook site.yml --ask-vault-pass
```
22. Galaxy

Install role:

```
ansible-galaxy role install geerlingguy.apache
```

List roles:

```
ansible-galaxy role list
```
23. Parallel Execution

Default:

```
forks = 5
```

Custom:

```
ansible all -m ping -f 20
```

Execute on 20 hosts simultaneously.

24. Limit Execution

Run only on one host:

```
ansible-playbook app.yml --limit web1
```

Run on group:

```
ansible-playbook app.yml --limit web
```
25. Become (Sudo)
```
become: yes
```

Run:

```
ansible-playbook app.yml --become
```

Specific user:

YAML
```
become_user: root
```
26. Debugging

Verbose mode:

```
ansible-playbook app.yml -v
```

More details:

```
ansible-playbook app.yml -vv
```

Very detailed:

```
ansible-playbook app.yml -vvv
```

Maximum:

```
ansible-playbook app.yml -vvvv
```
27. Common Modules

Copy:

```
copy:
```

Template:

```
template:
```

File:

```
file:
```

User:

```
user:
```

Package:

```
package:
```

Service:

```
service:
```

Command:

```
1
command:
```

Shell:

```
shell:
```

Git:

```
git:
```

Cron:

```
cron:
```

URI:
```
1
uri:
```

Docker:

```
1
docker_container:
```

Kubernetes:

```
1
k8s:
```
### 28. Real-Time Deployment Command

Deploy application:
```
ansible-playbook deploy.yml \
-i inventory \
-e version=1.0.5
```
Rollback:
```
ansible-playbook rollback.yml
```
### Top 20 Interview Questions
1. What is Ansible?
2. Difference between Playbook and Ad Hoc command?
3. What is Inventory?
4. What are Facts?
5. Difference between Command and Shell module?
6. What is Ansible Vault?
7. What are Roles?
8. What are Handlers?
9. What are Tags?
10. What is Idempotency?
11. How does Ansible connect to servers?
12. What is Dynamic Inventory?
13. Difference between Variables and Facts?
14. How do you secure passwords?
15. What is become?
16. What is Jinja2 Template?
17. What is Ansible Galaxy?
18. How do you run a playbook on specific hosts?
19. How do you troubleshoot playbook failures?
20 How do you deploy applications using Ansible?
21 Ansible Command Cheat Sheet
```
ansible --version
2
ansible all -m ping
3
ansible all -a "uptime"
4
ansible-playbook playbook.yml
5
ansible-playbook playbook.yml --check
6
ansible-playbook playbook.yml --syntax-check
7
ansible-playbook playbook.yml --list-hosts
8
ansible-playbook playbook.yml --list-tasks
9
ansible-playbook playbook.yml --tags install
10
ansible-playbook playbook.
```

These are the commands and concepts most commonly used by DevOps Engineers, SREs, Linux Administrators, and CI/CD engineers in production environments.

### Ansible Top 20 Interview Questions and Answers
Ansible Interview Questions & Answers
### 1. What is Ansible?

Ansible is an open-source IT automation and configuration management tool used for:

Configuration Management
Application Deployment
Infrastructure Provisioning
Orchestration

Key Features:

Agentless architecture
Uses SSH for connectivity
Written in Python
Uses YAML-based Playbooks

Example:

```
ansible all -m ping
```
2. Difference between Playbook and Ad Hoc Command?
Ad Hoc Command

Used for one-time tasks.

Example:

```
ansible webservers -m ping
```
```
ansible webservers -m yum -a "name=httpd state=present"
```
Playbook

Used for complex and repeatable automation.

Example:
```
YAML
1
---
2
- hosts: webservers
3
tasks:
4
- name: Install Apache
5
yum:
6
name: httpd
7
state: present
```
Difference
Ad Hoc	PlaybookOne-time execution	Reusable automation
Command line	YAML file
Simple tasks	Complex workflows
No version control	Can be stored in Git
### 3. What is Inventory?

Inventory is the list of managed hosts and groups.

Example:

```
1
[webservers]
2
192.168.1.10
3
192.168.1.11
4
 
5
[dbservers]
6
192.168.1.20
```

Check inventory:

```
ansible-inventory --list
```
4. What are Facts?

Facts are automatically gathered information about managed hosts.

Examples:

```
1
ansible_facts
```

Contains:

```
Hostname
2
IP Address
3
OS Version
4
Memory
5
CPU
6
Disk Information
```

View facts:

```
1
ansible all -m setup
```

Example usage:

```
1
- debug:
2
msg: "{{ ansible_hostname }}"
```
5. Difference between Command and Shell Module?
Command Module

Does not execute through shell.

```
1
- command: ls -l
```

Safer and preferred.

Shell Module

Executes through shell.

```
1
- shell: "ls -l | grep test"
```

Supports:

Pipes
Redirects
Environment variables
Example

Command won't work:

```
1
- command: cat file.txt | grep error
2
 
```

Shell works:

```
- shell: cat file.txt | grep error
```
6. What is Ansible Vault?

Ansible Vault encrypts sensitive information.

Examples:

Passwords
API Keys
Tokens
Certificates

Create encrypted file:

```
1
ansible-vault create secrets.yml
2
``
```

Edit:

```
1
ansible-vault edit secrets.yml
```

Encrypt existing file:

```
ansible-vault encrypt secrets.yml
```

Run playbook:

```
1
ansible-playbook site.yml --ask-vault-pass
```
7. What are Roles?

Roles organize playbooks into reusable structures.

Structure:

```
1
roles/
2
└── apache/
3
├── tasks/
4
├── handlers/
5
├── files/
6
├── templates/
7
├── vars/
8
└── defaults/
```

Use role:

```
1
- hosts: web
2
roles:
3
- apache
```

Benefits:

Reusability
Modularity
Easy maintenance
### 8. What are Handlers?

Handlers are tasks triggered only when notified.

Example:

```
1
- name: Update config
2
template:
3
src: httpd.conf.j2
4
dest: /etc/httpd/conf/httpd.conf
5
notify:
6
- restart apache
7

```

Handler:

```
handlers:
2
- name: restart apache
3
service:
4
name: httpd
5
state: restarted
```

Handler runs only when file changes.

## 9. What are Tags?

Tags run specific tasks in a playbook.

Example:

```
1
tasks:
2
- name: Install Apache
3
yum:
4
name: httpd
5
state: present
6
tags:
7
- install
```

Run:

```
ansible-playbook site.yml --tags install
```

Skip:

```
ansible-playbook site.yml --skip-tags install
```
10. What is Idempotency?

Idempotency means running the same playbook multiple times produces the same result.

Example:

```
1
- yum:
2
name: httpd
3
state: present
4

```

First run:

```
Package Installed
```

Second run:

```
No Changes
```

This ensures consistency and prevents unnecessary actions.

11. How does Ansible connect to servers?

Ansible uses:

SSH (Linux/Unix)
WinRM (Windows)

SSH example:

```
1
ansible all -m ping
```

Inventory:

```
1
web1 ansible_host=192.168.1.10 ansible_user=ec2-user
2
```

Authentication:

SSH Key
Username/Password

### 12. What is Dynamic Inventory?

Dynamic Inventory automatically fetches hosts from cloud providers.

Example Sources:

AWS EC2
Azure VMs
GCP Instances
VMware

Instead of:

INI
1
[web]
2
10.0.0.1
3
10.0.0.2
```

Ansible queries the cloud and discovers hosts automatically.

Example:

```
1
ansible-inventory -i aws_ec2.yml --list
```
13. Difference between Variables and Facts?
Variables

User-defined.

```
1
app_port: 8080
```
Facts

System-generated.

```
1
ansible_hostname
2
ansible_os_family
3
ansible_default_ipv4.address
```
Example:

```
1
- debug:
2
msg: "{{ app_port }}"
Show more lines
```
1
- debug:
2
msg: "{{ ansible_hostname }}"
```
14. How do you secure passwords?

Best practices:

Use Ansible Vault
```
1
ansible-vault encrypt secrets.yml
```
Store variables
```
1
db_password: MySecretPassword
2
 
```

Encrypted inside Vault.

Use external secret managers
HashiCorp Vault
AWS Secrets Manager
Azure Key Vault

### 15. What is become?

Used for privilege escalation.

Equivalent to:

```
sudo
```

Example:

```
1
- hosts: web
2
 
3
become: yes
4
 
5
tasks:
6
- service:
7
name: httpd
8
state: restarted
```

Run as root:

```
sudo
```

behind the scenes.

16. What is Jinja2 Template?

Jinja2 allows dynamic file generation.

Template:
```
Jinja
1
ServerName {{ hostname }}
2
 
3
Port {{ app_port }}
```

Playbook:

```
1
- template:
2
src: app.conf.j2
3
dest: /etc/app.conf
```

Generated file:

```
ServerName web01
2
Port 8080
3
 
```
### 17. What is Ansible Galaxy?

Ansible Galaxy is a repository for sharing roles and collections.

Install role:

```
ansible-galaxy role install geerlingguy.apache
```

Install collection:

```
ansible-galaxy collection install community.general
```
18. How do you run a playbook on specific hosts?
Inventory Group
```
ansible-playbook site.yml --limit webservers
```
Single Host
```
ansible-playbook site.yml --limit web01
```
Multiple Hosts
```
ansible-playbook site.yml --limit "web01,web02"
```
19. How do you troubleshoot playbook failures?
Increase Verbosity
```
ansible-playbook site.yml -v
```

More details:

```
ansible-playbook site.yml -vvv
```

Maximum:

```
ansible-playbook site.yml -vvvv
2
``
```
Check Connectivity
```
1
ansible all -m ping
```
Syntax Validation
```
1
ansible-playbook site.yml --syntax-check
```
Dry Run
```
ansible-playbook site.yml --check
```
Debug Variables
```
1
- debug:
2
var: ansible_hostname
```
20. How do you deploy applications using Ansible?

Typical workflow:

Install Packages
```
1
- yum:
2
name: java-17-openjdk
3
state: present
```
Copy Artifact
```
1
- copy:
2
src: app.jar
3
dest: /opt/app/app.jar
```
Configure Application
```
1
- template:
2
src: application.properties.j2
3
dest: /opt/app/application.properties
```
Start Service
```
1
- service:
2
name: app
3
state: started
```

Deployment Flow:

```
Build Application
2
↓
3
Copy Artifact
4
↓
5
Configure
6
↓
7
Start Service
8
↓
9
Validate
```
21. Ansible Command Cheat Sheet
Inventory
```
ansible-inventory --list
2
ansible-inventory --graph
```
Connectivity
```
ansible all -m ping
```
Run Command
```
ansible all -m command -a "uptime"
```
Run Shell
```
ansible all -m shell -a "df -h"
```
Copy File
```
ansible all -m copy -a "src=test.txt dest=/tmp/test.txt"
```
Install Package
```
ansible all -m yum -a "name=httpd state=present"
```
Gather Facts
```
ansible all -m setup
```
Execute Playbook
```
ansible-playbook site.yml
```
Syntax Check
```
ansible-playbook site.yml --syntax-check
```
Dry Run
```
ansible-playbook site.yml --check
```
Run with Tags
```
ansible-playbook site.yml --tags install
```
Limit Hosts
```
ansible-playbook site.yml --limit webservers
```
Vault
```
ansible-vault create secrets.yml
2
ansible-vault edit secrets.yml
3
ansible-vault encrypt secrets.yml
4
ansible-vault decrypt secrets.yml
```
Galaxy
```
ansible-galaxy role install geerlingguy.apache
2
ansible-galaxy collection install community.general
```
Verbose Debugging
```
ansible-playbook site.yml -v
2
ansible-playbook site.yml -vv
3
ansible-playbook site.yml -vvv
4
ansible-playbook site.yml -vvvv
```
### Quick Interview Summary
1. Ansible = Agentless automation tool.
2. Playbook = Reusable YAML automation.
3. Inventory = List of managed hosts.
4. Facts = System information gathered from hosts.
5. Roles = Reusable playbook components.
6. Handlers = Triggered tasks executed on change.
7. Vault = Encrypts secrets.
8. Tags = Run selected tasks.
9. Idempotency = Same result after multiple runs.
10. Become = Privilege escalation (sudo).
11. Jinja2 = Dynamic templates.
12. Galaxy = Repository for roles and collections.
13. Dynamic Inventory = Auto-discovered hosts from cloud providers.
