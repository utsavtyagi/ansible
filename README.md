<!-- ![image](https://github.com/user-attachments/assets/6113bcf7-d75e-45b0-965a-9f71a7a228e6) -->

<p align="center">
  <img src="https://github.com/user-attachments/assets/6113bcf7-d75e-45b0-965a-9f71a7a228e6" alt="Ansible"/>

</p>


---

# Ansible

## What You Will Learn
- How Ansible works
- How to install and configure Ansible
- Writing playbooks and inventories
- Variables, loops, conditions
- Windows automation using Ansible

---

## Table of Contents
1. [Introduction to Ansible](#1-introduction-to-ansible)
2. [Installation](#2-installation)
3. [Ansible Commands](#3-ansible-commands)
4. [Understanding YAML](#4-understanding-yaml)
5. [Ansible Inventory](#5-ansible-inventory)
6. [Ansible Playbooks](#6-ansible-playbooks)
7. [Ansible Variables](#7-ansible-variables)
8. [Ansible `when` Condition](#8-ansible-when-condition)
9. [Understanding `set_fact` in Ansible](#9-understanding-set_fact-in-ansible)
10. [Understanding `register` in Ansible](#10-understanding-register-in-ansible)
11. [Introduction to `loop` in Ansible](#11-introduction-to-loop-in-ansible)
12. [Ansible `Modules`](#12-ansible-modules)

---


## 1. Introduction to Ansible

Ansible is an open-source IT automation tool. It helps automate configuration management, application deployment, task automation, orchestration, and many other IT processes. It is free to use and it simplify operations and reduce complexity. It allows you to automate almost any task across your infrastructure.

Ansible uses simple, human-readable scripts known as playbooks. In a playbook, you define the desired state of a local or remote system, and Ansible ensures that the system remains in that state.

Ansible's design focuses on the following principles:

**Agent-less architecture:**  
Ansible does not require agents to be installed on managed nodes, minimizing maintenance overhead.

**Simplicity:**  
Playbooks use clear YAML syntax, making them easy to read and understand. Ansible connects to remote machines using SSH with existing OS credentials.

**Scalability and flexibility:**  
Ansible's modular design supports a wide range of operating systems, cloud platforms, and network devices, enabling easy scaling of automated tasks.

**Idempotence and predictability:**  
Ansible only makes changes when necessary. If the system already matches the desired state defined in the playbook, no changes are made, even if the playbook is run multiple times.

---

## 2. Installation

Ansible can be installed on:

**Linux:**  
This is the most common environment for Ansible. It works on various Linux distributions like Ubuntu, CentOS, Fedora, Rocky, and Red Hat Enterprise Linux (RHEL).

**macOS:**  
Ansible can be installed on macOS using Homebrew or pip (Python's package manager).

**Windows (via WSL):**  
Ansible is not directly supported on Windows as a control node, but you can install it using Windows Subsystem for Linux (WSL). This lets you run a Linux distribution inside Windows.

---

### Managed Nodes

![image](https://github.com/user-attachments/assets/93a9d476-6f66-4138-9b6a-b89fe459650a)

Ansible can manage a wide variety of systems, including:

- Linux and Unix systems (e.g., Ubuntu, CentOS, Debian, Red Hat)
- Windows systems (via WinRM or SSH)
- Cloud platforms (e.g., AWS, Azure, Google Cloud)
- Network devices (e.g., Cisco, Juniper, Arista switches)

In summary, while the Ansible control node is typically installed on a Unix-like environment (Linux or macOS), it can manage both Unix/Linux and Windows systems, providing flexibility in automation across different platforms.

**Control Node** :  
The machine where Ansible is installed and executed.

**Managed Nodes** :  
The machines that Ansible manages. These do not need Ansible installed.

---

### Ansible Installation Steps

This section explains how to install and prepare **Ansible Control Node (Linux)** and configure **Linux and Windows Managed Nodes**.  
The sequence follows real-world best practices:
1. Prepare Linux control node
2. Install Ansible
3. Install required dependencies
4. Configure Windows connectivity
5. Configure Linux SSH access

---

### Part 1: Prepare Ansible Control Node (Linux)

Below steps apply to **Red Hat-based Linux distributions** such as **Rocky, CentOS, RHEL, Fedora**.

---

#### Step 1: Update Linux Packages

Updates all installed packages on the system to their latest versions, including:
- Security patches  
- Kernel updates  
- System libraries  

This ensures system stability before installing Ansible.

```bash
sudo dnf update -y
````

---

### Step 2: Install EPEL Repository

EPEL (Extra Packages for Enterprise Linux) provides additional packages not available in the default repositories, including Ansible.

```bash
sudo dnf install epel-release -y
```

---

#### Step 3: Install Ansible

Installs **Ansible Core**, which includes:

* `ansible`
* `ansible-playbook`
* Core modules and plugins

```bash
sudo dnf install ansible-core -y
```

---

#### Step 4: Verify Ansible Installation

Check the installed Ansible version.

```bash
ansible --version
```

Verify the Ansible binary path.

```bash
which ansible
```

Verify the Ansible playbook binary path.

```bash
which ansible-playbook
```

Test whether Ansible can execute modules locally.

```bash
ansible localhost -m ping
```

Expected result: `pong`

---

### Part 2: Install Required Packages on Control Node

These packages are required for **SSH**, **Windows (WinRM)**, and **Kerberos authentication**.

---

#### Step 5: Install `sshpass`

Required when using **password-based SSH authentication** (not recommended for production, but useful for initial testing).

```bash
sudo dnf install -y sshpass
```

---

#### Step 6: Install Python Package Manager (`pip`)

`pip` is required to install Python libraries used by Ansible for Windows communication.

```bash
sudo dnf install -y python3-pip
```

---

#### Step 7: Install `pywinrm`

Enables Ansible to communicate with Windows hosts using **WinRM**.

```bash
sudo pip install pywinrm
```

Alternative (if `pip3` is required):

```bash
sudo pip3 install pywinrm
```

---

#### Step 8: Install Kerberos Dependencies

Required for **Kerberos-based WinRM authentication**, commonly used in enterprise environments.

```bash
sudo dnf install -y \
  krb5-workstation \
  python3-requests-kerberos \
  python3-gssapi \
  gcc \
  python3-devel \
  krb5-devel
```

---

#### Step 9: Install `pykerberos`

Provides Kerberos authentication support for Python.

```bash
python3 -m pip install --user pykerberos
```

---

#### Step 10: Install Ansible Windows Collection

Installs Windows-specific Ansible modules such as:

* `win_shell`
* `win_service`
* `win_copy`
* `win_reboot`

```bash
ansible-galaxy collection install ansible.windows
```

Verify installation:

```bash
ansible-galaxy collection list | grep ansible.windows
```

---

### Part 3: Configure Windows Managed Nodes

---

#### Step 11: Configure WinRM on Windows Servers

Run the official Ansible PowerShell script **on each Windows target server**.

🔗 **ConfigureRemotingForAnsible.ps1**
[https://github.com/ansible/ansible-documentation/blob/devel/examples/scripts/ConfigureRemotingForAnsible.ps1](https://github.com/ansible/ansible-documentation/blob/devel/examples/scripts/ConfigureRemotingForAnsible.ps1)

This script:

* Enables WinRM
* Configures firewall rules
* Sets authentication and encryption
* Prepares Windows for Ansible connectivity

---

### Part 4: Configure Linux Managed Nodes (SSH)

---

#### Step 12: Create a Dedicated Ansible User

Run on **each target Linux server**.

```bash
sudo useradd ansibleuser
sudo passwd ansibleuser
```

This user will be used exclusively by Ansible for automation.

---

#### Step 13: Configure Passwordless Sudo

Allows Ansible to execute privileged commands without prompting for a password.

```bash
sudo visudo
```

Add:

```text
ansibleuser ALL=(ALL) NOPASSWD: ALL
```

---

#### Step 14: Do NOT Add User to `wheel` Group

Avoid unnecessary administrative privileges.

```bash
sudo gpasswd -d ansibleuser wheel
```

⚠️ Do **NOT** run:

```bash
sudo usermod -aG wheel ansibleuser
```

---

#### Step 15: Generate SSH Key Pair (Control Node)

Run on the **Ansible control node**.

```bash
ssh-keygen -t rsa -b 4096
```

Generates:

* **Private Key:** `/home/utsavtyagi/.ssh/id_rsa` (keep secure)
* **Public Key:** `/home/utsavtyagi/.ssh/id_rsa.pub`

---

### Step 16: Copy Public Key to Target Linux Servers

```bash
ssh-copy-id ansibleuser@172.16.1.210
```

Repeat for all Linux target servers.

---

### Verification

Test passwordless SSH login:

```bash
ssh ansibleuser@TargetedLinuxServer
```

Login should succeed **without prompting for a password**.

---

## 3. Ansible Commands

This section covers **commonly used Ansible commands** that help you:
- Verify Ansible installation
- Test connectivity
- Run ad-hoc commands
- Execute modules without writing playbooks

These commands are frequently used during **setup, troubleshooting, and validation**.

---

### 1. Check Ansible Version

Displays the installed Ansible version, configuration file location, Python version, and module paths.

```bash
ansible --version
```

---

### 2. Test Localhost Connectivity

Tests whether Ansible can execute modules on the local machine.

```bash
ansible localhost -m ping
```

Expected output:

```text
localhost | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

---

### 3. Ping All Hosts in Inventory

Verifies connectivity to all managed hosts defined in the inventory file.

```bash
ansible all -m ping -i inventory.yml
```

This confirms:

* SSH (Linux) or WinRM (Windows) connectivity
* Authentication is working
* Python / PowerShell execution is successful

---

### 4. Run a Command on All Hosts

Executes a command on all target machines.

```bash
ansible all -m command -a "hostname" -i inventory.yml
```

Useful for:

* Verifying hostnames
* Quick system checks

---

### 5. Run Shell Commands (Linux Only)

Executes shell commands on Linux hosts.

```bash
ansible all -m shell -a "uptime" -i inventory.yml
```

---

### 6. Run Windows Commands

Runs a command on Windows hosts using WinRM.

```bash
ansible all -m win_command -a "hostname" -i inventory.yml
```

---

### 7. Gather System Facts

Collects detailed system information (OS, CPU, memory, disks, IPs).

```bash
ansible all -m setup -i inventory.yml
```

Facts are stored in variables like:

* `ansible_hostname`
* `ansible_os_family`
* `ansible_default_ipv4.address`

---

### 8. Check Inventory Hosts

Lists all hosts defined in the inventory.

```bash
ansible all --list-hosts -i inventory.yml
```

---


## 4. Understanding YAML

YAML (YAML Ain't Markup Language) is used for writing Ansible playbooks and configuration files.  
YAML is a human-readable data serialization language. It is commonly used for configuration files and in applications where data is being stored.  
YAML stands for yet another markup language or YAML ain’t markup language.

YAML focuses on **readability and simplicity**, which makes it ideal for infrastructure automation tools like Ansible.

---

### Why YAML for Ansible?

Ansible uses YAML because:
- It is easy to read and write
- It looks similar to plain English
- It avoids complex syntax like brackets and semicolons
- It clearly represents structured data

Almost everything in Ansible is written in YAML:
- Playbooks
- Inventories (YAML format)
- Variables
- Configuration files

---

### YAML Basics

#### YAML Example

---

#### 1. Key Value Pair

```yaml
---
Fruit: Mango
Vegetable: Tomato
Flower: Lotus
```

```yaml
---
app_name: CoreCredit
app_port: 443
environment: Production
```

**Use cases**

* Used for simple configuration settings (app name, port, environment)
* Values can be string, number, boolean
* Keys must be unique
* Common in application config files and Ansible variables

---

#### 2. Array / List

```yaml
---
Fruits:
  - Orange
  - Mango
  - Banana

Vegetables:
  - Tomato
  - Carrot
  - Potato

Flowers:
  - Lotus
  - Rose
  - Lily
```

```yaml
---
servers:
  - appserver01
  - appserver02
  - dbserver01

packages:
  - nginx
  - docker
  - git
```

**Key points**

* Used to store multiple items of the same type
* Order matters in lists
* Each item starts with `-`
* Commonly used for servers, packages, users, IPs

---

#### 3. Dictionary / Map

```yaml
---
Orange:
  Calories: 62
  Fat: 0.2 g
  Carbs: 15.4 g

Mango:
  Calories: 135
  Fat: 0.6 g
  Carbs: 35 g

Banana:
  Calories: 105
  Fat: 0.3 g
  Carbs: 27 g
```

```yaml
---
database:
  server: sqlprod01
  port: 1433
  name: CoreCreditDB
```

**Key points**

* Stores key-value pairs inside a parent object
* Used for structured configurations like database settings
* Keys inside a dictionary must be unique
* Dictionaries are unordered

---

#### 4. List of Dictionaries

```yaml
---
Fruits:
  - Orange:
      Calories: 62
      Fat: 0.2 g
      Carbs: 15.4 g

  - Mango:
      Calories: 135
      Fat: 0.6 g
      Carbs: 35 g

  - Banana:
      Calories: 105
      Fat: 0.3 g
      Carbs: 27 g
```

```yaml
---
websites:
  - name: CoreCredit
    port: 8080
    app_pool: CoreCredit_AppPool

  - name: WCFService
    port: 9090
    app_pool: WCF_AppPool
```

**Use cases**

* Used when each item has multiple properties
* Very common in DevOps and Ansible for websites, services, users, containers
* Each list item is a dictionary
* Ideal for defining complex configurations

---

### Additional YAML Concepts

---

#### 5. Boolean Values

```yaml
enabled: true
debug: false
```

Valid boolean values:

* `true / false`
* `yes / no`
* `on / off`

---

#### 6. Null / Empty Values

```yaml
value1: null
value2:
```

Used when a value is intentionally empty.

---

#### 7. Quoted vs Unquoted Strings

```yaml
path: /var/www/html
message: "Hello World"
port: "8080"
```

Use quotes when:

* Value contains special characters
* Value starts with a number but should be treated as a string

---

#### 8. Multi-line Strings

**Using `|` (preserves line breaks)**

```yaml
message: |
  This is line one
  This is line two
  This is line three
```

---

#### 9. Comments in YAML

```yaml
# This is a comment
app_name: CoreCredit  # Inline comment
```

Comments are ignored during execution.

---

### Common YAML Mistakes

❌ Wrong indentation
❌ Mixing tabs and spaces
❌ Missing `:` after keys
❌ Incorrect list formatting

YAML is **indentation-sensitive**. Use **spaces only**.

---

## 5. Ansible Inventory

The inventory file lists all hosts managed by Ansible.

---

### Inventory Examples

#### List of Servers

This is a simple list of servers without any grouping.

```yaml
---
all:
  hosts:
    server1.example.com:
    server2.example.com:
    server3.example.com:
```

---

#### Groups and Servers

This example includes groups and assigns servers to each group.

```yaml
---
AppGroup1:
  hosts:
    cclabapp01:
    cclabauth01:
    cclabwf01:

AppGroup2:
  hosts:
    cclabapp02:
    cclabauth02:
    cclabwf01:
```

---

#### Groups, Servers, and Parameters (Connection, Port, etc.)

This example specifies additional parameters like connection type, port, and custom variables.

```yaml
---
AppGroup1:
  hosts:
    cclabapp01:
    cclabauth01:
    cclabwf01:

AppGroup2:
  hosts:
    cclabapp02:
    cclabauth02:
    cclabwf01:

all:
  vars:
    ansible_connection: winrm
    ansible_winrm_server_cert_validation: ignore
    ansible_winrm_transport: kerberos
    ansible_winrm_port: 5986
    ansible_winrm_scheme: https
```

---

## 6. Ansible Playbooks

Playbooks define tasks to be executed on hosts. An Ansible Playbook is a configuration management and automation script written in YAML that defines a set of tasks for Ansible to execute on remote machines. Playbooks are the main way to organize and execute automation jobs in Ansible. They can include various tasks, handlers, variables, and other elements needed to manage systems, configure applications, or deploy services.

---

**Key Components of a Playbook:**

**Plays:**
A playbook consists of one or more "plays." Each play is an attempt to map a group of hosts to some tasks. A play runs tasks on selected machines based on the inventory.

**Tasks:**
Each play contains a list of tasks. A task defines an action to be performed on the target systems, such as installing a package, copying a file, or starting a service. Tasks use Ansible modules (like apt, yum, copy, etc.) to perform these actions.

**Hosts:**
Defines which machines (from the inventory) the tasks will be executed on. This can be a group of machines or a specific host.

**Variables:**
Variables can be used to define dynamic values such as paths, usernames, or any parameter that changes frequently. Variables can be set within the playbook or defined externally.

**Handlers:**
Handlers are special tasks that only run when notified by another task. Typically, handlers are used for things like restarting services after a configuration change.

---

### Playbook Example

##### Playbook:

```yaml
---
- name: Print the hostname of the Linux server
  hosts: all
  tasks:
    - name: Run echo command to display the hostname
      shell: echo "Hello! I am a Linux Server. My name is $HOSTNAME"

    - name: Install chrony
      shell: sudo dnf install chrony -y
```

* **name:** Describes what the playbook or task does. It's just for readability.
* **hosts:** Specifies the host or group of hosts to run the tasks on. Here, all is the host group defined in the Ansible inventory, You can specify any group specified in the inventory.
* **tasks:** Defines a list of tasks to execute on the hosts:

  ```
        - Run echo command to display the hostname.
        - Install chrony.
  ```

---

### Ansible Playbook Execution

You can run an Ansible playbook with the following command:

##### Command:

```bash
ansible-playbook Playbook.yml -i inventory.yml
```

* Playbook.yml is the playbook file name.
* inventory.yml is the inventory file name.

##### Command:

```bash
ansible-playbook Playbook.yml -i inventory.yml -e "Group='Group1'"
```

```bash
ansible-playbook Playbook.yml -i inventory.yml -e "Group='Group1, Group2'"
```

* "Group" is a extra variables we are passing in command.

---

## 7. Ansible Variables

**What Are Variables?**

Variables in Ansible are used to store values that can be reused throughout playbooks, making them more flexible and easier to manage. These values can include strings, integers, lists, dictionaries, and more.

---

**Basic Syntax**

Ansible variables are defined in YAML and are always referenced using double curly braces ({{ variable_name }}).

---

**Example:**

##### Playbook:

```yaml
vars:
  my_variable: "Hello, World!"
```

---

**Using a Variable**

##### Playbook:

```yaml
---
- name: Print the Variable
  hosts: all
  vars:
    my_variable: "Hello, World!"

  tasks:
    - name: Print variable
      debug:
        msg: "{{ my_variable }}"
```

##### Command:

```bash
ansible-playbook Playbook.yml -i inventory.yml
```

---

### Defining Variables

#### 1. Inside a Playbook:

##### Playbook:

```yaml
- name: Variable inside a playbook
- hosts: all
  vars:
    example_var: "Ansible is awesome!"
  tasks:
    - debug:
        msg: "{{ example_var }}"
```

##### Command:

```bash
ansible-playbook Playbook.yml -i inventory.yml
```

---

#### 2. In a Separate File (Vars File):

Create variables.yml:

```yaml
example_var: "Ansible from vars file!"
```

Use it in a playbook:

##### Playbook:

```yaml
---
- name: Variables in a separate file
  hosts: all
  vars_files:
    - variables.yml

  tasks:
    - name: Print variable
      debug:
        msg: "{{ example_var }}"
```

##### Command:

```bash
ansible-playbook Playbook.yml -i inventory.yml
```

---

#### 3. From the Command Line:

##### Playbook:

```yaml
---
- name: Variables from the command line
  hosts: all
  tasks:
    - name: Print variable
      debug:
        msg: "{{ example_var }}"
```

##### Command:

```bash
ansible-playbook Playbook.yml -i inventory.yml --extra-vars "example_var='CLI variable'"
```

OR

##### Command:

```bash
ansible-playbook Playbook.yml -i inventory.yml -e "example_var='CLI variable'"
```

---

### Types of Ansible Variables

**Common Types:**

#### 1. String:

```yaml
my_string: "Hello"
```

#### 2. Integer:

```yaml
my_number: 123
```

#### 3. Boolean:

```yaml
my_boolean: true
```

#### 4. List:

```yaml
my_list:
  - item1
  - item2
  - item3
```

#### 5. Dictionary:

```yaml
my_dict:
  key1: value1
  key2: value2
```

---

### Variable Precedence

Ansible applies variables based on a precedence hierarchy. Variables defined in certain places override others. Here's the order:

1. Extra variables in ansible-playbook command (highest precedence)
2. Task variables
3. Play variables
4. Inventory variables

---

## 8. Ansible `when` Condition

### Overview

The `when` condition in Ansible is used to control the execution of tasks based on specific conditions. This allows you to make tasks more dynamic and adaptable.

The when condition is a powerful feature in Ansible for task control. By using logical operators, variables, and facts, you can create flexible and efficient playbooks.

---

### Syntax

##### Playbook:

```yaml
---
- name: Example Task
  command: echo "Hello"
  when: <condition>
```

---

**Examples**

#### 1. Basic Condition

Run a task only when a variable equals a specific value.

##### Playbook:

```yaml
---
- name: Variables from the command line
  hosts: all
  vars:
    var1: "test"

  tasks:
    - name: Run only if var1 equals "test"
      debug:
        msg: "Condition met"
      when: var1 == "test"
```

---

#### 2. Multiple Conditions

Use and or or for complex conditions.

##### Playbook:

```yaml
- name: Run when both conditions are true
  command: echo "Both Conditions Met"
  when: var1 == "test" and var2 == "passed"

- name: Run when either condition is true
  command: echo "At Least One Condition Met"
  when: var1 == "test" or var2 == "failed"
```

---


## 9. Understanding `set_fact` in Ansible

### What is `set_fact`?

`set_fact` is used to create or modify variables dynamically during playbook execution. These variables, known as facts, persist for the duration of the play.

---

### Syntax for `set_fact`

##### Playbook:

```yaml
- name: Define a fact
  set_fact:
    variable_name: value
```

---

#### Example 1: Simple Fact Definition

##### Playbook:

```yaml
- name: Use set_fact to define a variable
  hosts: localhost
  tasks:
    - name: Set a fact
      set_fact:
        my_fact: "Hello from set_fact"

    - name: Display the fact
      debug:
        msg: "{{ my_fact }}"
```

##### Command:

```bash
ansible-playbook Playbook.yml -i inventory.yml
```

##### Output:

```output
TASK [Set a fact]
ok: [localhost]

TASK [Display the fact]
ok: [localhost] => {
    "msg": "Hello from set_fact"
}
```

---

#### Example 2: Updating a Fact's Value

You can perform calculations or manipulate data with set_fact.

##### Playbook:

```yaml
---
- name: Update a fact's value
  hosts: localhost
  tasks:
    - name: Set initial value
      set_fact:
        counter: 5

    - name: Increment the counter
      set_fact:
        counter: "{{ counter + 1 }}"

    - name: Multiply the counter
      set_fact:
        counter: "{{ counter * 2 }}"

    - name: Display the updated value
      debug:
        msg: "The updated counter is {{ counter }}"
```

##### Command:

```bash
ansible-playbook Playbook.yml -i inventory.yml
```

##### Output:

```output
TASK [Set initial value] 
ok: [localhost]

TASK [Display initial value] 
ok: [localhost] => {
    "msg": "Initial counter: 5"
}

TASK [Increment the counter] 
ok: [localhost]

TASK [Display after increment] 
ok: [localhost] => {
    "msg": "Counter after increment: 6"
}

TASK [Multiply the counter] 
ok: [localhost]

TASK [Display after multiplication] 
ok: [localhost] => {
    "msg": "Counter after multiplication: 24"
}
```

---

#### Example 3: Calculations with set_fact

You can perform calculations or manipulate data with set_fact.

##### Playbook:

```yaml
- name: Perform calculations with set_fact
  hosts: localhost
  tasks:
    - name: Set initial values
      set_fact:
        number1: 10
        number2: 20

    - name: Calculate the sum
      set_fact:
        sum: "{{ number1 + number2 }}"

    - name: Display the result
      debug:
        msg: "The sum is {{ sum }}"
```

##### Command:

```bash
ansible-playbook Playbook.yml -i inventory.yml
```

##### Output:

```output
TASK [Set initial values]
ok: [localhost]

TASK [Calculate the sum]
ok: [localhost]

TASK [Display the result]
ok: [localhost] => {
    "msg": "The sum is 30"
}
```

---

#### Example 4: Conditional Facts

Set facts based on conditions.

##### Playbook:

```yaml
---
- name: Conditional set_fact
  hosts: localhost
  gather_facts: false
  vars:
    environment_name: 'Development'

  tasks:
    - name: Set a fact conditionally
      set_fact:
        environment_details: "This is Development environment"
      when: environment_name == 'Development'

    - name: Set a fact conditionally
      set_fact:
        environment_details: "This is Production environment"
      when: environment_name == 'Production'

    - name: Print the environment
      debug:
        msg: "Environment: {{ environment_name }}"
```

##### Command:

```bash
ansible-playbook Playbook.yml -i inventory.yml
```

##### Output:

```output
PLAY [Conditional set_fact] 

ok: [localhost]

TASK [Set a fact conditionally] 
skipping: [localhost]

TASK [Print the environment] 
ok: [localhost] => {
    "msg": "Environment: Development"
}
```

---

## 10. Understanding `register` in Ansible

### What is `register`?

The `register` directive captures the output of a task and stores it in a variable. This variable contains detailed information such as stdout, stderr, return code, and more.

---

### Syntax for `register`

##### Playbook:

```yaml
- name: Run a task and register its output
  command: your_command_here
  register: variable_name
```

---

#### Example 1: Capture Command Output

##### Playbook:

```yaml
- name: Capture command output
  hosts: localhost
  gather_facts: false
  tasks:
    - name: Run the hostname command
      command: hostname
      register: command_output

    - name: Display the output
      debug:
        msg: "The hostname is {{ command_output.stdout }}"
```

##### Command:

```bash
ansible-playbook Playbook.yml -i inventory.yml
```

##### Output:

```output
TASK [Run the hostname command] 
changed: [localhost]

TASK [Display the output] 
ok: [localhost] => {
    "msg": "The hostname is {'changed': True, 'stdout': 'VMCCROCKY01', 'stderr': '', 'rc': 0, 'cmd': ['hostname'], 'start': '2024-12-06 12:32:41.347346', 'end': '2024-12-06 12:32:41.350822', 'delta': '0:00:00.003476', 'msg': '', 'stdout_lines': ['VMCCROCKY01'], 'stderr_lines': [], 'failed': False}"
}
```

---

#### Example 2: Using `register` with Conditions

You can make decisions based on the output captured using register.

##### Playbook:

```yaml
---
- name: Conditional task based on register
  hosts: localhost
  gather_facts: false
  tasks:
    - name: Check if a file exists
      stat:
        path: /home/utsav.tyagi/file1.txt
      register: file_check

    - name: Display file status
      debug:
        msg: "The file exists!"
      when: file_check.stat.exists
```

##### Command:

```bash
ansible-playbook Playbook.yml -i inventory.yml
```

##### Output:

```output
TASK [Check if a file exists] 
ok: [localhost]

TASK [Display file status] 
ok: [localhost] => {
    "msg": "The file exists!"
}
```

---

#### Registered Data Structure

The variable created by `register` contains the following structure:

* stdout: Standard output of the command.
* stderr: Standard error of the command.
* rc: Return code.
* changed: Boolean indicating if the task made changes.

---

## 11. Introduction to `loop` in Ansible

### What is `loop`?

`Loop` in Ansible allow you to execute a task multiple times with varying inputs. This is useful for repetitive actions like creating files, managing packages, or configuring users.

---

### Syntax for `loop`

##### Playbook:

```yaml
tasks:
  - name: Task with a loop
    <module>:
      parameter: "{{ item }}"
    loop:
      - item1
      - item2
      - item3
```

---

#### Example 1: Loop Example

##### Playbook:

```yaml
---
- name: Loop Example
  hosts: localhost
  gather_facts: false
  tasks:
    - name: loop example task
      debug:
        msg: "Watching {{ item }}"
      loop:
        - Netflix
        - Prime
        - Hotstar
```

##### Command:

```bash
ansible-playbook Playbook.yml -i inventory.yml
```

##### Output:

```output
TASK [loop example task] 
ok: [localhost] => (item=Netflix) => {
    "msg": "Displaying Netflix"
}
ok: [localhost] => (item=Prime) => {
    "msg": "Displaying Prime"
}
ok: [localhost] => (item=Hotstar) => {
    "msg": "Displaying Hotstar"
}
```

---

#### Example 2: Creating Multiple Files

##### Playbook:

```yaml
- name: Create multiple files on Windows
  hosts: all
  gather_facts: false
  tasks:
    - name: Create files
      win_file:
        path: C:\Temp\{{ item }}
        state: touch
      loop:
        - file1.txt
        - file2.txt
        - file3.txt
```

##### Command:

```bash
ansible-playbook Playbook.yml -i inventory.yml
```

##### Output:

```output
TASK [Create files] 
changed: [CCLABAPP01] => (item=file1.txt)
changed: [CCLABAPP01] => (item=file2.txt)
changed: [CCLABAPP01] => (item=file3.txt)
changed: [CCLABAPP02] => (item=file1.txt)
changed: [CCLABAPP02] => (item=file2.txt)
changed: [CCLABAPP02] => (item=file3.txt)
```

---

#### Example 3: Using Dictionaries in Loop
You can loop over dictionaries to handle more complex data structures.

##### Playbook:

```yaml
- name: Website Information
  hosts: localhost
  gather_facts: false
  tasks:
    - name: loop example task
      debug:
        msg: "Website {{ item.name }} - path {{ item.path }} - AppPool {{ item.AppPool }} - User {{ item.User }}"
      loop:
        - name: CoreCredit
          path: D:\WebServer\CoreCredit
          AppPool: CoreCredit_AppPool
          User: web_user

        - name: WCF
          path: D:\WebServer\WCF
          AppPool: WCF_AppPool
          User: wcf_user
```

##### Command:

```bash
ansible-playbook Playbook.yml -i inventory.yml
```

##### Output:

```output
TASK [loop example task] 
ok: [localhost] => (item={'name': 'CoreCredit', 'path': 'D:\\WebServer\\CoreCredit', 'AppPool': 'CoreCredit_AppPool', 'User': 'web_user'}) => {
    "msg": "Website CoreCredit - path D:\\WebServer\\CoreCredit - AppPool CoreCredit_AppPool - User web_user"
}
ok: [localhost] => (item={'name': 'WCF', 'path': 'D:\\WebServer\\WCF', 'AppPool': 'WCF_AppPool', 'User': 'wcf_user'}) => {
    "msg": "Website WCF - path D:\\WebServer\\WCF - AppPool WCF_AppPool - User wcf_user"
}
```

---

#### Example 4: Registering Output in Loop

##### Playbook:

```yaml
---
- name: Register outputs in Loop
  hosts: localhost
  gather_facts: false
  
  tasks:
    - name: Run commands and register outputs
      shell: echo "Hello {{ item }}"
      loop:
        - World
        - Ansible
        - Linux
      register: command_output

    - name: Display results
      debug:
        msg: "{{ item.stdout }}"
      loop: "{{ command_output.results }}"
```

##### Command:

```bash
ansible-playbook Playbook.yml -i inventory.yml
```

##### Output:

```output
TASK [Run commands and register outputs] 
changed: [localhost] => (item=World)
changed: [localhost] => (item=Ansible)
changed: [localhost] => (item=Linux)

TASK [Display results] 
ok: [localhost] => (item={'changed': True, 'stdout': 'Hello World', 'stderr': '', 'rc': 0, 'cmd': 'echo "Hello World"', 'start': '2024-12-06 13:41:43.393778', 'end': '2024-12-06 13:41:43.397435', 'delta': '0:00:00.003657', 'msg': '', 'invocation': {'module_args': {'_raw_params': 'echo "Hello World"', '_uses_shell': True, 'stdin_add_newline': True, 'strip_empty_ends': True, 'argv': None, 'chdir': None, 'executable': None, 'creates': None, 'removes': None, 'stdin': None}}, 'stdout_lines': ['Hello World'], 'stderr_lines': [], 'failed': False, 'item': 'World', 'ansible_loop_var': 'item'}) => {
    "msg": "Hello World"
}
ok: [localhost] => (item={'changed': True, 'stdout': 'Hello Ansible', 'stderr': '', 'rc': 0, 'cmd': 'echo "Hello Ansible"', 'start': '2024-12-06 13:41:43.671301', 'end': '2024-12-06 13:41:43.674841', 'delta': '0:00:00.003540', 'msg': '', 'invocation': {'module_args': {'_raw_params': 'echo "Hello Ansible"', '_uses_shell': True, 'stdin_add_newline': True, 'strip_empty_ends': True, 'argv': None, 'chdir': None, 'executable': None, 'creates': None, 'removes': None, 'stdin': None}}, 'stdout_lines': ['Hello Ansible'], 'stderr_lines': [], 'failed': False, 'item': 'Ansible', 'ansible_loop_var': 'item'}) => {
    "msg": "Hello Ansible"
}
ok: [localhost] => (item={'changed': True, 'stdout': 'Hello Linux', 'stderr': '', 'rc': 0, 'cmd': 'echo "Hello Linux"', 'start': '2024-12-06 13:41:44.213032', 'end': '2024-12-06 13:41:44.216680', 'delta': '0:00:00.003648', 'msg': '', 'invocation': {'module_args': {'_raw_params': 'echo "Hello Linux"', '_uses_shell': True, 'stdin_add_newline': True, 'strip_empty_ends': True, 'argv': None, 'chdir': None, 'executable': None, 'creates': None, 'removes': None, 'stdin': None}}, 'stdout_lines': ['Hello Linux'], 'stderr_lines': [], 'failed': False, 'item': 'Linux', 'ansible_loop_var': 'item'}) => {
    "msg": "Hello Linux"
}
```

---

#### Example 5: Conditional Loop Execution

##### Playbook:

```yaml
---
- name: Conditional tasks based on loop items
  hosts: CCLABAPP01
  gather_facts: false
  
  tasks:
    - name: Skip items based on condition
      win_shell: echo "Processing {{ item }}"
      loop:
        - item1
        - item2
        - skip_this
        - item3
      when: item != "skip_this"
```

##### Command:

```bash
ansible-playbook Playbook.yml -i inventory.yml
```

##### Output:

```output
TASK [Skip items based on condition] 
changed: [CCLABAPP01] => (item=item1)
changed: [CCLABAPP01] => (item=item2)
skipping: [CCLABAPP01] => (item=skip_this)
changed: [CCLABAPP01] => (item=item3)
```

---

### `label` and `loop_control` in `loop`

The label in loop_control is used to customize the label shown in the output of the task during a playbook run. This makes the output more meaningful and easier to understand, especially when dealing with loops that process multiple items.

By default, Ansible shows the `item` being processed in the output. Using `loop_control.label`, you can define a custom label for each loop iteration.

---

#### Key Features of label in loop_control:

* Custom Message: It replaces the default item output with a descriptive label.
* Dynamic Values: You can use variables like {{ item }} or other contextual data to make the label meaningful.
* Improves Readability: Helps in debugging by making the playbook's output more user-friendly.

---

##### With `label` and without `label`

##### Playbook:

```yaml
---
- name: Loop Example
  hosts: localhost
  gather_facts: false
  tasks:
    - name: loop without label
      shell: echo "Processing your request using {{ item }}"
      loop:
        - IIS
        - Apache
        - Nginx

    - name: loop example task
      shell: echo "Processing your request using {{ item }}"
      loop:
        - IIS
        - Apache
        - Nginx
      loop_control:
        label: "WebServer: {{ item }}"
```

##### Command:

```bash
ansible-playbook Playbook.yml -i inventory.yml
```

##### Output:

```output
TASK [loop without label] 
changed: [localhost] => (item=IIS)
changed: [localhost] => (item=Apache)
changed: [localhost] => (item=Nginx)

TASK [loop example task] 
changed: [localhost] => (item=WebServer: IIS)
changed: [localhost] => (item=WebServer: Apache)
changed: [localhost] => (item=WebServer: Nginx)
```

---

## 12. Ansible `Modules`

Ansible provides a variety of modules specifically designed for managing systems. These modules allow administrators to perform tasks like installing software, managing services, handling files, and running PowerShell commands efficiently.

[Ansible Collections](https://docs.ansible.com/projects/ansible/latest/collections/index.html)

Here’s a comprehensive tutorial on common Windows-specific Ansible modules with practical examples.

---

### **1. `win_command`: Running Windows Commands**

The `win_command` module runs arbitrary commands on Windows hosts without using a shell.

**Example: Running a Simple Command**

```yaml
---
- name: Run a command on a Windows host
  hosts: all
  gather_facts: false
  tasks:
    - name: Display the Windows hostname
      win_command: hostname
      register: result

    - name: Display the Result
      debug:
        var: result.stdout
```

#### Command:

```bash
ansible-playbook Playbook.yml -i inventory.yml
```

#### Output:

```output
TASK [Display the Windows hostname] 
changed: [CCLABAPP01]
changed: [CCLABAPP02]

TASK [Display the Result] 
ok: [CCLABAPP01] => {
    "result.stdout": "cclabapp01\r\n"
}
ok: [CCLABAPP02] => {
    "result.stdout": "cclabapp02\r\n"
}
```

---

### **2. `win_shell`: Executing Commands in a Shell**

The `win_shell` module runs commands in a Windows shell, allowing for more complex command execution.

**Example: Run a Batch Script**

```yaml
---
- name: Run a PowerShell Command 
  hosts: all
  gather_facts: false
  tasks:
    - name: Test the Connection
      win_shell: Test-NetConnection -ComputerName localhost -port 80
      register: result

    - name: Display the Result
      debug:
        var: result.stdout
```

#### Command:

```bash
ansible-playbook Playbook.yml -i inventory.yml
```

#### Output:

```output
TASK [Test the Connection] 
changed: [CCLABAPP01]
changed: [CCLABAPP02]

TASK [Display the Result] 
ok: [CCLABAPP01] => {
    "result.stdout": "\r\n\r\nComputerName     : localhost\r\nRemoteAddress    : ::1\r\nRemotePort       : 80\r\nInterfaceAlias   : Loopback Pseudo-Interface 1\r\nSourceAddress    : ::1\r\nTcpTestSucceeded : True\r\n\r\n\r\n\r\n"
}
ok: [CCLABAPP02] => {
    "result.stdout": "\r\n\r\nComputerName           : localhost\r\nRemoteAddress          : ::1\r\nRemotePort             : 80\r\nInterfaceAlias         : Loopback Pseudo-Interface 1\r\nSourceAddress          : ::1\r\nPingSucceeded          : True\r\nPingReplyDetails (RTT) : 0 ms\r\nTcpTestSucceeded       : False\r\n\r\n\r\n\r\n"
}
```

---

### **3. `win_service`: Managing Windows Services**

The `win_service` module starts, stops, restarts, or ensures the state of a Windows service.

**Example: Ensure the Windows Update Service is Running**

```yaml
---
- name: Run a PowerShell Command Windows 
  hosts: all
  gather_facts: false
  tasks:
    - name: Start Windows Update service
      win_service:
        name: wuauserv
        state: started
      register: result

    - name: Display the Result
      debug:
        var: result.stdout
```

#### Command:

```bash
ansible-playbook Playbook.yml -i inventory.yml
```

#### Output:

```output
TASK [Start Windows Update service] 
ok: [CCLABAPP01]
ok: [CCLABAPP02]

TASK [Display the Result] 
ok: [CCLABAPP01] => {
    "result": {
        "can_pause_and_continue": false,
        "changed": false,
        "depended_by": [],
        "dependencies": [
            "rpcss"
        ],
        "description": "Enables the detection, download, and installation of updates for Windows and other programs. If this service is disabled, users of this computer will not be able to use Windows Update or its automatic updating feature, and programs will not be able to use the Windows Update Agent (WUA) API.",
        "desktop_interact": false,
        "display_name": "wuauserv",
        "exists": true,
        "failed": false,
        "name": "wuauserv",
        "path": "C:\\Windows\\system32\\svchost.exe -k netsvcs -p",
        "start_mode": "auto",
        "state": "running",
        "username": "LocalSystem"
    }
}
ok: [CCLABAPP02] => {
    "result": {
        "can_pause_and_continue": false,
        "changed": false,
        "depended_by": [],
        "dependencies": [
            "rpcss"
        ],
        "description": "Enables the detection, download, and installation of updates for Windows and other programs. If this service is disabled, users of this computer will not be able to use Windows Update or its automatic updating feature, and programs will not be able to use the Windows Update Agent (WUA) API.",
        "desktop_interact": false,
        "display_name": "Windows Update",
        "exists": true,
        "failed": false,
        "name": "wuauserv",
        "path": "C:\\Windows\\system32\\svchost.exe -k netsvcs -p",
        "start_mode": "auto",
        "state": "running",
        "username": "LocalSystem"
    }
}
```

---

### **4. `win_package`: Installing or Removing Software**

The `win_package` module handles installation or removal of software on Windows.

**Example: Install Software**

```yaml
---
- name: Install software using win_package
  hosts: all
  gather_facts: false
  tasks:
    - name: Install Google Chrome
      win_package:
        path: "C:\\Temp\\ChromeStandaloneSetup64.exe"
        state: present
      register: result

    - name: Display the Result
      debug:
        var: result
```

#### Command:

```bash
ansible-playbook Playbook.yml -i inventory.yml
```

#### Output:

```output
TASK [Install Google Chrome] 
changed: [CCLABAPP02]
changed: [CCLABAPP01]

TASK [Display the Result] 
ok: [CCLABAPP01] => {
    "result": {
        "changed": true,
        "failed": false,
        "rc": 0,
        "reboot_required": false
    }
}
ok: [CCLABAPP02] => {
    "result": {
        "changed": true,
        "failed": false,
        "rc": 0,
        "reboot_required": false
    }
}
```

---

### **5. `win_copy`: Copying Files to Windows Hosts**

The `win_copy` module copies files or directories from the Ansible control machine to Windows hosts.

**Example: Copy a File to a Windows Host**

```yaml
---
- name: Copy a file to a Windows host
  hosts: all
  gather_facts: false
  tasks:
    - name: Copy a file
      win_copy:
        src: /home/utsav.tyagi/ChromeStandaloneSetup64.exe
        dest: C:\Temp\ChromeStandaloneSetup64.exe
        remote_src: false
      register: result

    - name: Display the Result
      debug:
        var: result
```

#### Command:

```bash
ansible-playbook Playbook.yml -i inventory.yml
```

#### Output:

```output
TASK [Copy a file] 
ok: [CCLABAPP01]
changed: [CCLABAPP02]

TASK [Display the Result] 
ok: [CCLABAPP01] => {
    "result": {
        "changed": false,
        "checksum": "cf28c050fd21ad09de542ce51cbfa9b8f4609070",
        "dest": "C:\\Temp\\ChromeStandaloneSetup64.exe",
        "failed": false,
        "operation": "file_copy",
        "original_basename": "ChromeStandaloneSetup64.exe",
        "size": 126284672,
        "src": "/home/utsav.tyagi/ChromeStandaloneSetup64.exe"
    }
}
ok: [CCLABAPP02] => {
    "result": {
        "changed": true,
        "checksum": "cf28c050fd21ad09de542ce51cbfa9b8f4609070",
        "dest": "C:\\Temp\\ChromeStandaloneSetup64.exe",
        "failed": false,
        "operation": "file_copy",
        "original_basename": "ChromeStandaloneSetup64.exe",
        "size": 126284672,
        "src": "/home/utsav.tyagi/ChromeStandaloneSetup64.exe"
    }
}
```

---

### **6. `win_file`: Managing File and Directory States**

The `win_file` module manages file and directory creation, and deletion.

**Example: Create a Directory**

```yaml
---
- name: Create a directory on a Windows host
  hosts: all
  gather_facts: false
  tasks:
    - name: Ensure directory exists
      win_file:
        path: C:\Temp\NewDirectory
        state: directory
      register: result

    - name: Display the Result
      debug:
        var: result
```

#### Command:

```bash
ansible-playbook Playbook.yml -i inventory.yml
```

#### Output:

```output
TASK [Ensure directory exists] 
changed: [CCLABAPP01]
changed: [CCLABAPP02]

TASK [Display the Result] 
ok: [CCLABAPP01] => {
    "result": {
        "changed": true,
        "failed": false
    }
}
ok: [CCLABAPP02] => {
    "result": {
        "changed": true,
        "failed": false
    }
}
```

---

### **7. `win_reboot`: Rebooting a Windows Host**

The `win_reboot` module reboots a Windows host and waits for it to come back online.

**Example: Reboot**

```yaml
---
- name: Reboot Windows host
  hosts: all
  gather_facts: false
  tasks:
    - name: Reboot the server
      win_reboot:
        reboot_timeout: 600
      register: result

    - name: Display the Result
      debug:
        var: result
```

#### Command:

```bash
ansible-playbook Playbook.yml -i inventory.yml
```

#### Output:

```output
TASK [Reboot the server] 
changed: [CCLABAPP02]
changed: [CCLABAPP01]

TASK [Display the Result] 
ok: [CCLABAPP01] => {
    "result": {
        "changed": true,
        "elapsed": 36,
        "failed": false,
        "rebooted": true,
        "unreachable": false
    }
}
ok: [CCLABAPP02] => {
    "result": {
        "changed": true,
        "elapsed": 26,
        "failed": false,
        "rebooted": true,
        "unreachable": false
    }
}
```

---

## Conclusion

This course provides a foundation for getting started with Ansible. Explore the official Ansible documentation for more advanced features and modules.

[Ansible Documents](https://docs.ansible.com/projects/ansible/latest/)

---
