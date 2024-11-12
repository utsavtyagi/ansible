
# Ansible

## 1. Introduction to Ansible
Ansible is an open-source IT automation tool. It helps automate configuration management, application deployment, task automation, orchestration, and many other IT processes. It is free to use and it simplify operations and reduce complexity. It allows you to automate almost any task across your infrastructure.

Ansible uses simple, human-readable scripts known as playbooks. In a playbook, you define the desired state of a local or remote system, and Ansible ensures that the system remains in that state.

Ansible's design focuses on the following principles:

**Agent-less architecture:** Ansible does not require agents to be installed on managed nodes, minimizing maintenance overhead.

**Simplicity:** Playbooks use clear YAML syntax, making them easy to read and understand. Ansible connects to remote machines using SSH with existing OS credentials.

**Scalability and flexibility:** Ansible's modular design supports a wide range of operating systems, cloud platforms, and network devices, enabling easy scaling of automated tasks.

**Idempotence and predictability:** Ansible only makes changes when necessary. If the system already matches the desired state defined in the playbook, no changes are made, even if the playbook is run multiple times.


### Installation
To install Ansible on a Linux system:
```bash
sudo apt update
sudo apt install ansible -y
```

## 2. Understanding YAML
YAML (YAML Ain't Markup Language) is used for writing Ansible playbooks and configuration files.

### YAML Example
```yaml
---
name: John Doe
age: 30
address:
  city: New York
  zipcode: 10001
```

### Key Points
- Indentation matters.
- Use `:` for key-value pairs.
- Use `-` for lists.

## 3. Ansible Inventory
The inventory file lists all hosts managed by Ansible. It can be static or dynamic.

### Static Inventory Example
```ini
[webservers]
web1.example.com
web2.example.com

[dbservers]
db1.example.com
```

## 4. Inventory Formats
Ansible supports different formats for inventory:
- **INI format** (default)
- **YAML format**
- **Dynamic inventory** using scripts or cloud plugins.

### YAML Inventory Example
```yaml
all:
  hosts:
    web1.example.com:
    web2.example.com:
  children:
    dbservers:
      hosts:
        db1.example.com:
```

## 5. Grouping and Parent-Child Relationships
You can create nested groups for easier management.

### Example
```ini
[all_servers:children]
webservers
dbservers

[webservers]
web1.example.com

[dbservers]
db1.example.com
```

## 6. Ansible Variables and Facts
Variables store data that can be used in playbooks. Facts are system information gathered by Ansible.

### Variable Example
```yaml
vars:
  app_version: "1.2.3"
```

### Using Facts
```yaml
tasks:
  - name: Print system information
    debug:
      msg: "The OS version is {{ ansible_distribution_version }}"
```

## 7. Ansible Playbooks
Playbooks define tasks to be executed on hosts.

### Playbook Example
```yaml
---
- name: Install NGINX on web servers
  hosts: webservers
  tasks:
    - name: Install NGINX
      apt:
        name: nginx
        state: present
```

## 8. Ansible Modules and Plugins
Modules are the building blocks of Ansible. Plugins extend Ansible functionality.

### Using a Module Example
```yaml
- name: Create a file
  hosts: all
  tasks:
    - name: Create a new file
      file:
        path: /tmp/testfile.txt
        state: touch
```

## 9. Ansible Handlers
Handlers are triggered when tasks change the system state.

### Example
```yaml
tasks:
  - name: Install Apache
    apt:
      name: apache2
      state: present
    notify:
      - Restart Apache

handlers:
  - name: Restart Apache
    service:
      name: apache2
      state: restarted
```

## 10. Ansible Templates
Templates use Jinja2 syntax for dynamic configuration.

### Template Example (nginx.conf.j2)
```jinja2
server {
    listen 80;
    server_name {{ domain_name }};
    location / {
        proxy_pass http://{{ backend_server }};
    }
}
```

### Using the Template
```yaml
- name: Configure NGINX
  template:
    src: nginx.conf.j2
    dest: /etc/nginx/nginx.conf
```

## 11. Ansible Roles and Collections
Roles are a way to organize playbooks into reusable components.

### Creating a Role
```bash
ansible-galaxy init myrole
```

### Directory Structure
```
myrole/
├── tasks
│   └── main.yml
├── templates
├── vars
└── handlers
```

### Using a Role in Playbook
```yaml
- hosts: all
  roles:
    - myrole
```

## Conclusion
This course provides a foundation for getting started with Ansible. Explore the official Ansible documentation for more advanced features and modules.
