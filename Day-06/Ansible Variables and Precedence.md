**Ansible Variables and Precedence**

**Introduction**

In this guide, we will explore **Ansible variables** and their precedence, a crucial concept for writing dynamic and flexible automation playbooks. Understanding how variables work and where to define them is essential for infrastructure automation, particularly when provisioning cloud resources.

As a **real-world use case**, we will demonstrate how to use Ansible to **create an AWS EC2 instance** using the **Ansible AWS collection**. This will showcase how variables help **improve reusability, security, and maintainability** of Ansible Playbooks.

---

**Understanding Ansible Collections**

Ansible **collections** extend Ansible’s functionality by providing **pre-built modules, plugins, and roles** for different platforms. Instead of writing complex scripts, you can use collections to interact with **AWS, Azure, GCP, Kubernetes, Cisco devices**, and more.

•	Built-in Ansible modules (like apt, yum, and copy) execute tasks directly on managed nodes.

•	Collections (like **Amazon AWS, Kubernetes, Cisco, etc.**) install modules on the control node and interact with APIs.

This is particularly useful for **infrastructure automation**, where API interactions are required instead of SSH-based operations.

---

**Setting Up the Ansible AWS Collection**

To create AWS resources using Ansible, we must **install the AWS collection** and its dependencies.

**Step 1: Install the Ansible AWS Collection**

Run the following command to install the AWS collection:

```sh
ansible-galaxy collection install amazon.aws
```

**Step 2: Install the Required Python Library (boto3)**

AWS API calls require the **boto3** library. Install it using:

```sh
pip install boto3
```

Now, Ansible can communicate with AWS services through API calls.

---

**Securing AWS Credentials with Ansible Vault**

Never hardcode AWS **Access Keys and Secret Keys** in Ansible Playbooks. Instead, use **Ansible Vault** to store credentials securely.

**Step 1: Generate a Secure Vault Password**

```sh
openssl rand -base64 32 > vault.pass
```

**Step 2: Create a Vault File**

Run the command below to create a **vault-protected variables file:**

```sh
ansible-vault create aws_credentials.yml
```

Inside this file, store your AWS credentials:

```sh
aws_access_key: "YOUR_AWS_ACCESS_KEY"
aws_secret_key: "YOUR_AWS_SECRET_KEY"
```

**Step 3: Encrypt the Vault File**

```sh
ansible-vault encrypt aws_credentials.yml
```

Now, whenever you run your Playbook, pass the Vault password file:

```sh
ansible-playbook create_ec2.yml --vault-password-file vault.pass
```

---

**Creating an EC2 Instance with Ansible**

**Step 1: Define Inventory File**

Even though we are working with **AWS APIs**, it’s best practice to maintain an inventory file.

```sh
[local]
localhost ansible_connection=local
```

**Step 2: Create an Ansible Playbook (create_ec2.yml)**

```sh
---
- name: Provision an AWS EC2 Instance
  hosts: localhost
  connection: local
  vars_files:
    - aws_credentials.yml
  tasks:
    - name: Launch an EC2 instance
      amazon.aws.ec2_instance:
        name: "Ansible-Managed-EC2"
        key_name: "my-key"
        instance_type: "t2.micro"
        security_group: "default"
        region: "us-east-1"
        image_id: "ami-0c55b159cbfafe1f0"
        access_key: "{{ aws_access_key }}"
        secret_key: "{{ aws_secret_key }}"
      register: ec2_instance

    - name: Display EC2 Public IP
      debug:
        msg: "EC2 Public IP: {{ ec2_instance.instances[0].public_ip_address }}"
```

Now, run the Playbook:

```sh
ansible-playbook create_ec2.yml --vault-password-file vault.pass
```

---

**Ansible Variables and Precedence**

Ansible allows variables to be defined in multiple places, **but not all have the same priority**. The order of precedence (from **lowest to highest**) is:

**1.	Role Defaults** (defaults/main.yml) → Lowest priority

**2.	Inventory Group Variables** (group_vars/)

**3.	Inventory Host Variables** (host_vars/)

**4.	Role Variables** (vars/main.yml)

**5.	Playbook Variables (vars section in playbook)**

**6.	Extra Variables** (--extra-vars or -e) → Highest priority

**Example: Using Variables in Different Places**

**1. Define Variables in defaults/main.yml**

```sh
instance_type: "t2.micro"
region: "us-east-1"
```

**2. Override in vars/main.yml**

```sh
instance_type: "t2.medium"
```

**3. Override in Playbook (create_ec2.yml)**

```sh
vars:
  instance_type: "t2.large"
```

**4. Override at Execution Time**

```sh
ansible-playbook create_ec2.yml -e "instance_type=t2.xlarge"
```

**•	Defaults** (defaults/main.yml) are overridden by **vars** (vars/main.yml).

**•	Playbook variables override role variables.**

**•	Extra variables (-e) override everything.**

---

**Best Practices for Using Variables**

**1.	Store Defaults in** defaults/main.yml – Provides a base configuration.

**2.	Use** vars/main.yml **for Project-Specific Overrides** – Allows customization.

**3.	Use Inventory Group Variables for Environment-Specific Values** – Example: Different instance types for Dev, QA, and Prod.

**4.	Secure Secrets with Ansible Vault** – Never hardcode credentials.

**5.	Use Extra Variables (-e) for Temporary Overrides** – Useful for ad-hoc changes.

**6.	Follow the Jinja2 Templating Standard** – Use {{ variable_name }} syntax.

---

**Conclusion**

Understanding **Ansible variables and precedence** is key to writing **scalable and reusable automation playbooks**. By properly structuring variables:

•	We **avoid hardcoding values.**

•	We make our Playbooks **more flexible.**

•	We improve **security using Ansible Vault.**

•	We use **Ansible collections to manage AWS infrastructure** efficiently.

This knowledge is essential for **DevOps engineers, cloud architects, and SREs** working with **Ansible, AWS, and infrastructure automation.**

🚀 **Want to contribute?** Check out our GitHub Repository and start collaborating on Ansible projects.
