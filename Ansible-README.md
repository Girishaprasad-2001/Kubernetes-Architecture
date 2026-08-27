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
1. What is Ansible?
Answer:

Ansible is an open-source IT automation and configuration management tool used for:

Configuration Management
Application Deployment
Infrastructure Provisioning
Orchestration
Key Features:

Agentless
Uses SSH/WinRM
YAML-based Playbooks
Easy to learn and maintain
Interview Answer:

Ansible is an agentless automation tool that uses SSH to manage servers and automate configuration management, application deployment, and infrastructure provisioning.

2. Difference between Playbook and Ad Hoc Command?
Ad Hoc Command
Used for one-time tasks.




Shell
ansible all -m ping
Playbook
Used for complex, repeatable automation tasks.




Shell
ansible-playbook webserver
Difference
Ad Hoc	Playbook
One-time task	Reusable automation
Command line	YAML file
Quick execution	Complex workflows
Interview Answer:

Ad Hoc commands are used for quick one-time operations, while Playbooks are YAML-based files used for reusable and complex automation workflows.

3. What is Inventory?
Answer:

Inventory is a file that contains the list of managed hosts.

Example:




INI
[web]
server1
server2
 
[db]
db1
Interview Answer:

Inventory is a collection of managed nodes organized into groups that Ansible uses to determine where tasks should be executed.

4. What are Facts?
Answer:

Facts are system information automatically gathered by Ansible.

Example:




YAML
{{ ansible_hostname }}
{{ ansible_ip_addresses }}
{{ ansible_os_family }}
View facts:




Shell
ansible all -m setup
Interview Answer:

Facts are automatically collected details about managed hosts such as hostname, IP address, operating system, memory, and disk information.

5. Difference between Command and Shell Module?
Command Module



YAML
- command: ls -l
Does not support:




Shell
|
>
<
&&
Shell Module



YAML
- shell: "cat file.txt | grep test"
Supports shell operations.

Difference
Command	Shell
Safer	More flexible
No shell processing	Executes through shell
Faster	Slightly slower
Interview Answer:

The command module executes commands directly without shell interpretation, while the shell module executes commands through the shell and supports pipes, redirects, and environment variables.

6. What is Ansible Vault?
Answer:

Ansible Vault encrypts sensitive data.

Example:




Shell
ansible-vault create secrets.yml
``
Encrypt:




Shell
ansible-vault encrypt secrets.yml
Decrypt:




Shell
ansible-vault decrypt secrets.yml
Interview Answer:

Ansible Vault is used to securely store sensitive information such as passwords, API keys, and certificates by encrypting files and variables.

7. What are Roles?
Answer:

Roles organize playbooks into reusable components.

Structure:




Plain Text
roles/
 ├── webserver
      ├── tasks
      ├── handlers
      ├── templates
      ├── vars
      └── defaults
Use:




YAML
roles:
  - webserver
Interview Answer:

Roles provide a standardized way to organize and reuse Ansible code using predefined directories for tasks, templates, handlers, and variables.

8. What are Handlers?
Answer:

Handlers execute only when notified.

Example:




YAML
tasks:
  - name: Update Config
    template:
      src: app.conf.j2
      dest: /etc/app.conf
    notify: restart service
 
handlers:
  - name: restart service
    service:
      name: nginx
      state: restarted
Interview Answer:

Handlers are special tasks that run only when triggered by other tasks, commonly used for service restarts after configuration changes.

9. What are Tags?
Answer:

Tags allow selective task execution.

Example:




YAML
tasks:
  - name: Install Apache
    yum:
      name: httpd
    tags:
      - install
``
Run:




Shell
ansible-playbook site.yml --tags install
Interview Answer:

Tags help execute specific tasks or groups of tasks instead of running the entire playbook.

10. What is Idempotency?
Answer:

Idempotency means running the same playbook multiple times produces the same result.

Example:




YAML
- name: Install Nginx
  yum:
    name: nginx
    state: present
Ansible installs only if not already present.

Interview Answer:

Idempotency ensures that repeated execution of a playbook does not create duplicate changes and always maintains the desired state.

11. How does Ansible connect to servers?
Answer:

Linux:




Plain Text
SSH
Windows:




Plain Text
WinRM
Ansible is agentless and requires no software installation on target servers.

Interview Answer:

Ansible connects to Linux systems using SSH and Windows systems using WinRM without requiring agents on managed nodes.

12. What is Dynamic Inventory?
Answer:

Dynamic inventory generates host lists automatically from cloud providers.

Example:




Shell
aws_ec2.yaml
Sources:

AWS
Azure
GCP
VMware
Interview Answer:

Dynamic inventory retrieves host information dynamically from cloud environments instead of maintaining static inventory files.

13. Difference between Variables and Facts?
Variables
Defined by users.




YAML
app_port: 8080
Facts
Automatically discovered.




YAML
ansible_hostname
Interview Answer:

Variables are user-defined values used in playbooks, while facts are automatically collected system information from managed hosts.

14. How do you secure passwords?
Answer:

Methods:

Ansible Vault
HashiCorp Vault
Azure Key Vault
AWS Secrets Manager
Example:




Shell
ansible-vault encrypt secrets.yml
Interview Answer:

Passwords should never be hardcoded. I secure them using Ansible Vault or enterprise secret-management solutions like HashiCorp Vault or cloud-native secret stores.

15. What is become?
Answer:

Used for privilege escalation.

Example:




YAML
tasks:
  - name: Install Package
    yum:
      name: httpd
      state: present
    become: yes
Equivalent:




Shell
sudo
Interview Answer:

The become directive allows Ansible to execute tasks with elevated privileges, typically using sudo on Linux systems.

16. What is Jinja2 Template?
Answer:

Jinja2 templates create dynamic configuration files.

Template:




Jinja
server_name={{ hostname }}
Playbook:




YAML
template:
  src: nginx.conf.j2
  dest: /etc/nginx/nginx.conf
`
Interview Answer:

Jinja2 templates enable dynamic file generation by replacing variables and expressions during playbook execution.

17. What is Ansible Galaxy?
Answer:

A repository for sharing roles and collections.

Install role:




Shell
ansible-galaxy role install geerlingguy.nginx
Interview Answer:

Ansible Galaxy is a community repository used to discover, download, and share reusable roles and collections.

18. How do you run a playbook on specific hosts?
Using Limit



Shell
ansible-playbook site.yml --limit web
Specific server:




Shell
ansible-playbook site.yml --limit server1
``
Interview Answer:

I use the --limit option to target specific hosts or inventory groups when executing playbooks.

19. How do you troubleshoot playbook failures?
Increase Verbosity



Shell
ansible-playbook site.yml -v
More details:




Shell
ansible-playbook site.yml -vvv
Maximum debug:




Shell
ansible-playbook site.yml -vvvv
Check:




Shell
ansible all -m ping
Syntax validation:




Shell
ansible-playbook site.yml --syntax-check
Dry run:




Shell
ansible-playbook site.yml --check
Interview Answer:

I troubleshoot failures using verbose logging (-vvv), syntax checks, connectivity validation, task debugging, and check mode before making changes.

20. How do you deploy applications using Ansible?
Typical Flow



Plain Text
Copy Artifact
      ↓
Install Dependencies
      ↓
Configure Application
      ↓
Start Service
      ↓
Health Check
Example:




YAML
- hosts: app
 
  tasks:
 
    - name: Copy WAR File
      copy:
        src: app.war
        dest: /opt/tomcat/webapps/
 
    - name: Restart Tomcat
      service:
        name: tomcat
        state: restarted
Interview Answer:

I use Ansible playbooks to automate application deployment by copying artifacts, configuring servers, managing services, deploying application packages, and performing validation checks after deployment.

2-Minute Interview Summary
"Ansible is an agentless automation tool that uses SSH or WinRM to manage infrastructure. Core concepts include inventories, playbooks, variables, facts, roles, handlers, tags, and templates. It supports idempotent automation, secure secret management through Ansible Vault, dynamic inventories for cloud environments, and application deployments through reusable playbooks and roles."
