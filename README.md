![image](https://github.com/user-attachments/assets/6113bcf7-d75e-45b0-965a-9f71a7a228e6)

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

Ansible can be installed on:

**Linux**: This is the most common environment for Ansible. It works on various Linux distributions like Ubuntu, CentOS, Fedora, and Red Hat Enterprise Linux (RHEL).

**macOS**: Ansible can be installed on macOS using Homebrew or pip (Python's package manager).

**Windows (via WSL)**: Ansible is not directly supported on Windows as a control node, but you can install it using Windows Subsystem for Linux (WSL). This lets you run a Linux distribution inside Windows.


**Managed Nodes**
Ansible can manage a wide variety of systems, including:

Linux and Unix systems (e.g., Ubuntu, CentOS, Debian, Red Hat)

Windows systems (via WinRM or SSH)

Cloud platforms (e.g., AWS, Azure, Google Cloud)

Network devices (e.g., Cisco, Juniper, Arista switches)

In summary, while the Ansible control node is typically installed on a Unix-like environment (Linux or macOS), it can manage both Unix/Linux and Windows systems, providing flexibility in automation across different platforms.


**Ansible Installation Steps**

Below are the steps to install Ansible on a Red Hat-based Linux distribution (e.g., CentOS, RHEL, Fedora).

Step 1: Install EPEL (Extra Packages for Enterprise Linux) Repository

EPEL provides additional packages, including Ansible.

```bash
sudo dnf install epel-release -y
```

Step 2: Install Ansible (After enabling EPEL, install Ansible.)

```bash
sudo dnf install ansible -y
```

Step 3: Install Python Package Manager (pip)

Python's package manager is required to install additional Python modules like pywinrm for managing Windows systems.

```bash
sudo dnf install python3-pip -y
```

Step 4: Install pywinrm Using pip

The pywinrm module allows Ansible to communicate with Windows hosts via WinRM (Windows Remote Management).

```bash
sudo pip install pywinrm
```

Verify Ansible Installation

Check the installed version of Ansible to confirm the setup.

```bash
ansible --version
```



## 2. Understanding YAML

YAML (YAML Ain't Markup Language) is used for writing Ansible playbooks and configuration files.
YAML is a human-readable data serialization language. It is commonly used for configuration files and in applications where data is being stored.
YAML stands for yet another markup language or YAML ain’t markup language.

### YAML Example
```yaml
---
Fruit: Mango
Vegetable: Tomato
Flower: Lotus
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
