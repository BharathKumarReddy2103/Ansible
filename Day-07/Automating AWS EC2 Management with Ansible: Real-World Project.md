**Automating AWS EC2 Management with Ansible: Real-World Project**

**Introduction**

In this guide, we will explore **real-world automation with Ansible**, focusing on AWS EC2 instances. This tutorial is inspired by a **technical interview assignment**, which involves:

**1.	Creating EC2 instances using Ansible loops**

**2.	Setting up passwordless authentication**

**3.	Automating instance shutdown using conditionals**

By following this guide, you will learn **Ansible loops, conditionals, AWS automation**, and best practices for managing cloud resources efficiently.

---

**Prerequisites**

Before we begin, ensure you have:

**•	Ansible installed**

**•	AWS CLI configured with access keys**

**•	Boto3 installed** (pip install boto3)

**•	Ansible AWS Collection** installed:

```sh
ansible-galaxy collection install amazon.aws
```

---

**Project Overview**

This automation involves **three key tasks:**

**1.	Provisioning EC2 instances**: Use Ansible to create **three EC2 instances**—two **Ubuntu** and one **Amazon Linux.**

**2.	Passwordless Authentication**: Configure SSH authentication using key pairs.

**3.	Automating Shutdown**: Use **Ansible conditionals** to shut down only **Ubuntu** instances.

**Architecture Diagram**

```sh
[ Ansible Control Node (Laptop) ]
             │
             ▼
     [ AWS API via Boto3 ]
             │
             ▼
   ┌───────────────────────┐
   │ AWS EC2 Instances     │
   │ ┌────────┬──────────┐ │
   │ │ Ubuntu │ Ubuntu   │ │
   │ └────────┴──────────┘ │
   │        Amazon Linux   │
   └───────────────────────┘
```

---

**Step 1: Provisioning EC2 Instances with Ansible Loops**

**1.1 Create an AWS IAM User for Ansible**

**1.**	Go to **AWS Console > IAM > Users.**

**2.**	Create a new user named **ansible-user.**

**3.**	Assign **AmazonEC2FullAccess** policy.

**4.**	Generate an **Access Key & Secret Key.**

**5.**	Store credentials in **Ansible Vault** (explained later).

**1.2 Ansible Playbook to Create EC2 Instances**

Create a new **Ansible Playbook** (ec2_create.yaml) to launch three instances.

```sh
---
- name: Provision EC2 Instances
  hosts: localhost
  connection: local
  gather_facts: no

  vars:
    aws_region: "ap-south-1"
    key_name: "my-keypair"
    instance_type: "t2.micro"
    instances:
      - name: "Ubuntu-1"
        ami: "ami-12345678"  # Replace with latest Ubuntu AMI
      - name: "Ubuntu-2"
        ami: "ami-12345678"
      - name: "AmazonLinux"
        ami: "ami-87654321"  # Replace with latest Amazon Linux AMI

  tasks:
    - name: Launch EC2 Instances
      amazon.aws.ec2_instance:
        name: "{{ item.name }}"
        state: running
        region: "{{ aws_region }}"
        instance_type: "{{ instance_type }}"
        key_name: "{{ key_name }}"
        image_id: "{{ item.ami }}"
        network:
          assign_public_ip: true
      loop: "{{ instances }}"
```

**1.3 Run the Playbook**

Execute the playbook with:

```sh
ansible-playbook ec2_create.yaml
```

This will create three EC2 instances.

---

**Step 2: Configure Passwordless Authentication**

To enable **passwordless SSH access**, we use ssh-copy-id.

**1.**	Retrieve the **Public IPs** of created instances:

```sh
aws ec2 describe-instances --query 'Reservations[*].Instances[*].[PublicIpAddress]' --output text
```

2.	Copy the SSH key to each instance:

```sh
ssh-copy-id -i ~/.ssh/my-keypair.pem ubuntu@<IP>
ssh-copy-id -i ~/.ssh/my-keypair.pem ec2-user@<IP>
```

To **test SSH login**:

```sh
ssh ubuntu@<IP>
```

If successful, no password prompt should appear.

---

**Step 3: Automate Shutdown of Ubuntu Instances**

**3.1 Ansible Playbook to Stop Ubuntu Instances**

Create a new **Ansible Playbook** (ec2_stop.yaml):

```sh
---
- name: Shutdown Ubuntu Instances
  hosts: all
  become: true

  tasks:
    - name: Shutdown if OS is Ubuntu
      ansible.builtin.command: /sbin/shutdown -h now
      when: ansible_facts['os_family'] == "Debian"
```

**3.2 Run the Playbook**

```sh
ansible-playbook -i inventory.ini ec2_stop.yaml
```

This will **shut down only Ubuntu instances.**

---

**Best Practices**

**•	Use Ansible Vault** to store AWS credentials securely.

```sh
ansible-vault create aws_credentials.yaml
```

**•	Use Elastic IPs** to prevent changing IPs after restart.

**•	Test Playbooks in a Dev environment before production.**

**•	Monitor EC2 costs** using AWS Cost Explorer.

---

**Conclusion**

This guide covered **real-world AWS automation using Ansible**, including:

✔️ **EC2 provisioning with loops**

✔️ **Passwordless SSH authentication**

✔️ **Automated shutdown with conditionals**

By mastering these concepts, you can **automate cloud infrastructure**, improve **CI/CD pipelines**, and **prepare for DevOps interviews.**

📌 **Explore the Code on GitHub**

💬 **Got Questions?** Drop a comment below.


