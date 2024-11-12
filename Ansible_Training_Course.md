
# Ansible Training Course

## 1. Introduction to Ansible
Ansible is an open-source IT automation tool. It helps automate configuration management, application deployment, and task automation.

### Key Features
- Agentless: No need to install agents on nodes.
- Simple YAML-based configuration.
- Extensive collection of built-in modules.

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
