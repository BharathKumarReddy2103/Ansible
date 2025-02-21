**Ansible Components Complete Guide**

**Ansible** is an open-source automation tool that enables DevOps engineers to automate infrastructure provisioning, configuration management, application deployment, and other IT processes. 

**Below is a deep dive into Ansible's key components and how they work together.**

---

**1. Ansible Core Components**

These are the fundamental building blocks of Ansible.

**1.1 Control Node**

•	The system where Ansible is installed and executed.

•	It does not require any agents on the managed nodes.

•	Uses SSH (Linux) or WinRM (Windows) to communicate with remote machines.

**1.2 Managed Nodes (Hosts)**

•	These are the target machines where automation tasks are executed.

•	Defined in the inventory file.

•	Do not require Ansible to be installed (just SSH access and Python).

---

**2. Inventory**

The inventory is a file where Ansible stores details about managed nodes.

**Types of Inventory Files**

**1.	Static Inventory** (e.g., inventory.ini):

```bash
[web_servers]
192.168.1.10
192.168.1.11

[db_servers]
192.168.1.20
192.168.1.21
```

o	Groups hosts under categories like web_servers and db_servers.

**2.	Dynamic Inventory:**

o	Uses scripts or cloud APIs (e.g., AWS, Azure) to fetch inventory dynamically.

o	Example: AWS EC2 dynamic inventory using a Python script.

---

**3. Ansible Configuration File** (ansible.cfg)

•	Defines global configurations like inventory location, logging, privilege escalation (sudo), and SSH connection settings.

•	Example:

```bash
[defaults]
inventory = ./inventory
remote_user = ubuntu
private_key_file = ~/.ssh/id_rsa
```

---

**4. Modules**

Modules are reusable, standalone scripts that execute tasks on managed nodes.

**Types of Modules**

**1.	Core Modules** – Installed by default (e.g., copy, yum, apt, service).

**2.	Custom Modules** – Created by users in Python, Bash, or PowerShell.

**3.	Cloud Modules** – AWS (ec2_instance), Azure (azure_rm_virtualmachine), GCP (gcp_compute_instance).

**Example Module Usage in Playbook**

```bash
- name: Install Apache on Ubuntu
  hosts: web_servers
  tasks:
    - name: Install Apache
      apt:
        name: apache2
        state: present
```

---

**5. Tasks**

•	A task is a single operation executed on a managed node.

•	Each task uses a module.

•	Tasks are declared inside playbooks.

**Example:**

```bash
tasks:
  - name: Ensure Nginx is installed
    apt:
      name: nginx
      state: present
```

---

**6. Playbooks**

•	YAML files containing a set of tasks to automate a process.

•	Define the execution order of tasks.

Example:

```bash
- name: Configure Web Server
  hosts: web_servers
  become: yes
  tasks:
    - name: Install Apache
      apt:
        name: apache2
        state: present
    - name: Start Apache
      service:
        name: apache2
        state: started
```

---

**7. Roles**

Roles allow you to organize playbooks into reusable components.

**Structure of a Role**

```bash
roles/
  webserver/
    tasks/
      main.yml
    handlers/
      main.yml
    templates/
      index.html.j2
    files/
      sample.conf
    vars/
      main.yml
    defaults/
      main.yml
```

**Example Role Execution in a Playbook**

```bash
- name: Deploy Web App
  hosts: web_servers
  roles:
    - webserver
```

---

**8. Files**

•	Static files that need to be copied to remote machines.

•	Used for configuration files, scripts, or binaries.

Example File Copying

```bash
tasks:
  - name: Copy Nginx config file
    copy:
      src: files/nginx.conf
      dest: /etc/nginx/nginx.conf
```

•	src: Path to the local file.

•	dest: Path on the managed node.

---

**9. Variables**

•	Variables allow customization and reusability of configurations.

**Defining Variables**

```bash
vars:
  package_name: nginx
tasks:
  - name: Install web server
    apt:
      name: "{{ package_name }}"
      state: present
```

**Using Variables in Playbooks**

```bash
- name: Install web package
  hosts: web_servers
  vars:
    web_package: nginx
  tasks:
    - name: Install Web Server
      apt:
        name: "{{ web_package }}"
        state: present
```

---

**10. Facts**

•	Automatically gathered system information.

•	Used to conditionally execute tasks.

**Example of Gathering Facts**

```bash
tasks:
  - name: Display OS info
    debug:
      msg: "The OS is {{ ansible_distribution }} version {{ ansible_distribution_version }}"
```

**Common Facts**

•	ansible_distribution: OS name (Ubuntu, CentOS, etc.).

•	ansible_distribution_version: OS version.

•	ansible_hostname: Hostname of the managed node.

•	ansible_memtotal_mb: Total memory in MB.

---

**11. Templates (Jinja2)**

•	Used to create dynamic configuration files.

Example Template (nginx.conf.j2):

```bash
server {
  listen 80;
  server_name {{ server_name }};
  root {{ document_root }};
}
```

Example Playbook:

```bash
tasks:
  - name: Generate Nginx config
    template:
      src: nginx.conf.j2
      dest: /etc/nginx/nginx.conf
```

---

**12. Handlers**

•	Triggered when a task reports a change.

•	Used for services that need restarting.

Example:

```bash
tasks:
  - name: Install Nginx
    apt:
      name: nginx
      state: present
    notify: Restart Nginx

handlers:
  - name: Restart Nginx
    service:
      name: nginx
      state: restarted
```

---

**13. Tags**

•	Used to run specific tasks within a playbook.

Example:

```bash
tasks:
  - name: Install Apache
    apt:
      name: apache2
      state: present
    tags: install

  - name: Restart Apache
    service:
      name: apache2
      state: restarted
    tags: restart
```

Run only install tasks:

```bash
ansible-playbook webserver.yml --tags install
```

---

**14. Ansible Vault**

•	Encrypts sensitive data like passwords and SSH keys.

**Encrypt a File**

```bash
ansible-vault encrypt secrets.yml
```

**Decrypt a File**

```bash
ansible-vault decrypt secrets.yml
```

**Use Encrypted Variables in Playbooks**

```bash
vars:
  db_password: !vault |
    $ANSIBLE_VAULT;1.1;AES256
    324f....
```

---

**15. Callback Plugins**

•	Extend Ansible’s reporting functionality.

•	Examples: JSON output, Slack notifications.

---

**16. Connection Plugins**

•	Default: SSH.

•	Others: Paramiko, Docker, WinRM.

---

**17. Lookup Plugins**

•	Fetch external data sources (files, databases, environment variables).

```bash
Example:
tasks:
  - name: Read a file
    debug:
      msg: "{{ lookup('file', '/etc/motd') }}"
```

---

**Conclusion**

Ansible is a powerful, agentless automation tool with a modular structure. By understanding these core components, you can efficiently automate IT infrastructure and DevOps tasks.
