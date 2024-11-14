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


## 2. Installation

Ansible can be installed on:

**Linux**: This is the most common environment for Ansible. It works on various Linux distributions like Ubuntu, CentOS, Fedora, and Red Hat Enterprise Linux (RHEL).

**macOS**: Ansible can be installed on macOS using Homebrew or pip (Python's package manager).

**Windows (via WSL)**: Ansible is not directly supported on Windows as a control node, but you can install it using Windows Subsystem for Linux (WSL). This lets you run a Linux distribution inside Windows.


**Managed Nodes**

![image](https://github.com/user-attachments/assets/93a9d476-6f66-4138-9b6a-b89fe459650a)


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



## 3. Understanding YAML

YAML (YAML Ain't Markup Language) is used for writing Ansible playbooks and configuration files.
YAML is a human-readable data serialization language. It is commonly used for configuration files and in applications where data is being stored.
YAML stands for yet another markup language or YAML ain’t markup language.

### YAML Example

#### Key Value Pair
```yaml
---
Fruit: Mango
Vegetable: Tomato
Flower: Lotus
```

#### Array/List
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
  - Lotis
  - Rose
  - Lily
```

#### Dictionary/Map
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

#### List of dictionaries
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


### Key Points
- Indentation matters.
- Use `:` for key-value pairs.
- Use `-` for lists.
- Dictionary is unordered collectiom
- List is ordered collection (order of items matters)
- Use "#" to comment any line


## 4. Ansible Inventory
The inventory file lists all hosts managed by Ansible.

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



## 5. Ansible Playbooks
Playbooks define tasks to be executed on hosts. An Ansible Playbook is a configuration management and automation script written in YAML that defines a set of tasks for Ansible to execute on remote machines. Playbooks are the main way to organize and execute automation jobs in Ansible. They can include various tasks, handlers, variables, and other elements needed to manage systems, configure applications, or deploy services.

**Key Components of a Playbook:**

**Plays:** A playbook consists of one or more "plays." Each play is an attempt to map a group of hosts to some tasks. A play runs tasks on selected machines based on the inventory.

**Tasks:** Each play contains a list of tasks. A task defines an action to be performed on the target systems, such as installing a package, copying a file, or starting a service. Tasks use Ansible modules (like apt, yum, copy, etc.) to perform these actions.

**Hosts:** Defines which machines (from the inventory) the tasks will be executed on. This can be a group of machines or a specific host.

**Variables:** Variables can be used to define dynamic values such as paths, usernames, or any parameter that changes frequently. Variables can be set within the playbook or defined externally.

**Handlers:** Handlers are special tasks that only run when notified by another task. Typically, handlers are used for things like restarting services after a configuration change.

### Playbook Example
```yaml
---
---
- name: Print the hostname of the Linux server
  hosts: all
  tasks:
    - name: Run echo command to display the hostname
      shell: echo "Hello! I am a Linux Server. My name is $HOSTNAME"

    - name: Install chrony
      shell: sudo dnf install chrony -y
```
- **name:** Describes what the playbook or task does. It's just for readability.

- **hosts:** Specifies the host or group of hosts to run the tasks on. Here, all is the host group defined in the Ansible inventory, You can specify any group specified in the inventory.

- **tasks:** Defines a list of tasks to execute on the hosts:

            - Run echo command to display the hostname.
            - Install chrony.


### Ansible Playbook Execution:

You can run an Ansible playbook with the following command:

```bash
ansible-playbook Playbook.yml -i inventory.yml
```

- Playbook.yml is the playbook file name.
- inventory.yml is the inventory file name.

```bash
ansible-playbook Playbook.yml -i inventory.yml -e "Group='Group1'"
```

- "MachineGroupName='Group1'" is a extra variables we are passing in command.


## Conclusion
This course provides a foundation for getting started with Ansible. Explore the official Ansible documentation for more advanced features and modules.
