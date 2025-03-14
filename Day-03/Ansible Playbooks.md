**Ansible Playbooks**

**Introduction**

Ansible Playbooks are the heart of **Ansible automation**, allowing DevOps engineers to define tasks and orchestrate complex deployments in a structured manner. Before diving into **Ansible Playbooks**, it is crucial to understand **YAML (Yet Another Markup Language)**, as Ansible Playbooks are written in YAML.

In this guide, we will cover:

**•	Introduction to YAML** and why it is preferred over JSON and text files

**•	Understanding Ansible Playbooks**, including its structure and components

**•	Building our first Ansible Playbook** to configure a web server and deploy a static website

**•	Best practices** for writing Ansible Playbooks

By the end of this guide, you will have a solid foundation in **Ansible Playbooks** and how to use them effectively for automation.

---

**What is YAML?**

**YAML (Yet Another Markup Language)** is a human-readable data serialization format used widely in DevOps for configuration management, CI/CD, and Kubernetes manifests.

**Why Use YAML Over Text Files or JSON?**

**1.	Structured Format:** Unlike text files, YAML follows a consistent and standardized structure.

**2.	Human-Readable:** YAML is easier to read and write compared to JSON, as it does not require **curly braces ({}) or commas ( ,).**

**3.	Used in DevOps Tools:** Many DevOps tools, such as **Ansible, Kubernetes, and GitHub Actions**, rely on YAML for configurations.

**YAML Syntax Basics**

**1.	Key-Value Pairs**

```sh
name: Bharath Kumar Reddy
age: 30
is_active: true
```

**2.	Lists in YAML**

```sh
tasks:
   - Deploy application
	 - Configure web server
	 - Monitor logs
```

**3.	Dictionaries (Key-Value Nested Structures)**

```sh
address:
   street: "123 DevOps Lane"
   city: "Cloud City"
   country: "India"
```

**4.	List of Dictionaries**

```sh
users:
   - name: Alice
     role: Developer
   - name: Bob
     role: DevOps Engineer
```
 
With these basic YAML concepts, let's now move on to **Ansible Playbooks.**

---

**Understanding Ansible Playbook Structure**

An **Ansible Playbook** is a YAML file that contains a series of **plays** to be executed on remote machines. A **play** consists of **tasks** that use various **Ansible modules** to perform actions.

**Playbook Structure**

```sh
---
- name: Deploy Web Server
  hosts: all
  become: true

  tasks:
    - name: Install Apache Web Server
      ansible.builtin.apt:
        name: apache2
        state: present

    - name: Deploy Static Website
      ansible.builtin.copy:
        src: index.html
        dest: /var/www/html/index.html
        owner: root
        group: root
        mode: "0644"
```

**Key Components of an Ansible Playbook**

**1.	Hosts:** Specifies the target machines where the playbook will be executed.

**2.	Become:** Runs tasks with elevated privileges (similar to sudo).

**3.	Tasks:** A list of actions to be executed, using **Ansible modules** like apt (for installing packages) and copy (for copying files).

---

**Deploying a Web Server with Ansible Playbook**

Now, let’s apply what we’ve learned by creating an **Ansible Playbook** to install an **Apache web server** and deploy a simple **static website.**

**1. Create a Playbook File**

Save the following YAML content as webserver-playbook.yaml:

```sh
---
- name: Deploy Web Server
  hosts: all
  become: true

  tasks:
    - name: Install Apache Web Server
      ansible.builtin.apt:
        name: apache2
        state: present

    - name: Deploy Static Website
      ansible.builtin.copy:
        src: index.html
        dest: /var/www/html/index.html
        owner: root
        group: root
        mode: "0644"

    - name: Start and Enable Apache Service
      ansible.builtin.service:
        name: apache2
        state: started
        enabled: true
```

**2. Create an HTML File**

Create a simple HTML page and save it as index.html:

```sh
<!DOCTYPE html>
<html>
<head>
    <title>My Ansible Deployed Website</title>
</head>
<body>
    <h1>Welcome to My Web Server</h1>
    <p>Deployed using Ansible Playbooks!</p>
</body>
</html>
```

**3. Run the Ansible Playbook**

Execute the playbook using the following command:

```sh
ansible-playbook -i inventory.ini webserver-playbook.yaml
```

Ensure that your **inventory file** (inventory.ini) contains the list of remote servers:

```sh
[web_servers]
server1 ansible_host=192.168.1.100 ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/id_rsa
server2 ansible_host=192.168.1.101 ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/id_rsa
```

---

**Verifying the Deployment**

**1.	Check if Apache is running:**

```sh
sudo systemctl status apache2
```

**2.	Access the Web Page:**

Open a browser and enter the **public IP** of the target machine:

```sh
http://<your-ec2-instance-ip>
```

You should see your static website deployed successfully.

---

**Best Practices for Writing Ansible Playbooks**

**1.	Use Roles for Scalability**

o	Instead of writing large playbooks, break them into **roles** for better modularity.

o	Example:

```sh
ansible-galaxy init webserver
```

**2.	Keep Playbooks Idempotent**

o	Ensure tasks **don’t repeat unnecessary actions** if they’ve already been executed.

**3.	Use Variables and Templates**

o	Use vars and **Jinja2 templates** to make playbooks dynamic.

**4.	Store Playbooks in Git**

o	Use **GitHub/GitLab** to maintain version control and CI/CD automation.

**5.	Test Playbooks Locally with ansible-lint**

o	Run the following command to check for errors:

```sh
ansible-lint webserver-playbook.yaml
```

---

**Conclusion**

In this guide, we covered:

✅ **Introduction to YAML** and why it’s crucial for Ansible

✅ **Ansible Playbook structure** and components like plays, tasks, and modules

✅ **Step-by-step deployment of a web server using Ansible Playbook**

✅ **Best practices for writing efficient and scalable Playbooks**

By mastering **Ansible Playbooks**, you can automate cloud infrastructure, manage servers efficiently, and simplify DevOps workflows.

🔗 **Further Reading**

•	**Ansible Playbook Documentation** (https://docs.ansible.com/ansible/latest/playbook_guide/playbooks.html)

•	**Ansible Built-in Modules** (https://docs.ansible.com/ansible/latest/collections/ansible/builtin/index.html)

•	**YAML Syntax Guide** (https://yaml.org/)

If you found this guide useful, **star** ⭐ **the GitHub repository** and **contribute** with improvements or additional examples.
