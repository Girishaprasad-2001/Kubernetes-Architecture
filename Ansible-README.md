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
# Ansible Top 20 Interview Questions and Answers

## 1. What is Ansible?

### Answer
Ansible is an open-source IT automation and configuration management tool used for:

- Configuration Management
- Application Deployment
- Infrastructure Provisioning
- Orchestration

### Key Features

- Agentless
- Uses SSH/WinRM
- YAML-based Playbooks
- Easy to learn and maintain

### Interview Answer
Ansible is an agentless automation tool that uses SSH to manage servers and automate configuration management, application deployment, and infrastructure provisioning.

---

## 2. Difference between Playbook and Ad Hoc Command?

### Ad Hoc Command

Used for one-time tasks.

```bash
ansible all -m ping
```

### Playbook

Used for complex, repeatable automation tasks.

```bash
ansible-playbook webserver.yml
```

### Difference

| Ad Hoc | Playbook |
|----------|----------|
| One-time task | Reusable automation |
| Command line | YAML file |
| Quick execution | Complex workflows |

### Interview Answer

Ad Hoc commands are used for quick one-time operations, while Playbooks are YAML-based 
