Project Overview

We will provision Apache2 on two remote servers and MySQL on two remote servers using Ansible.

My name is Rubben, and I'll be your instructor for this project. Let's begin.

Ansible Project

What is Ansible?

Ansible is an open-source automation tool for provisioning infrastructure on one or more systems, whether in the cloud, on-premises, or hybrid. Ansible is used for tasks such as configuration management, application deployment, and infrastructure setup.

Setting Up the Controller

The Ansible Controller is the machine where we install Ansible to run commands and playbooks. This can be a laptop, desktop, virtual machine, or cloud instance (e.g., EC2). On this controller, we will create two files: the Ansible Inventory and the Ansible Playbook.

Ansible Inventory: This file contains information about our servers (hosts), including IP addresses or domain names and connection details. The inventory file uses INI format and groups servers based on their roles. Here's an example:

graphql
Copy code
[database_servers]
ipnx@172.31.28.157
ubuntu@172.31.28.157
web_app@172.31.26.237

[application_servers]
ipnx@172.31.28.157
ubuntu@172.31.28.157
web_app@172.31.26.237
Ansible Playbook: This file contains the tasks that Ansible will execute on the host machines. It is written in YAML format, which uses key-value pairs and lists.

Setting Up the Hosts

Ansible is agentless, so we don't need to install any client apps on the host machines. We'll use SSH for connections.

Steps to Begin

Launch Instances: Spin up 5 instances on AWS—one as the controller and four as hosts. Install Ansible on the controller.

Create Project Directory: On the controller, create a directory named 'ansible-project' and inside it, create the inventory and playbook files:

bash
Copy code
mkdir ansible-project
cd ansible-project
touch hosts playbook.yml
Install Ansible: On the controller machine, run:

bash
Copy code
sudo apt update
sudo apt install software-properties-common
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install ansible
ansible --version
Create Inventory File: Navigate to the 'ansible-project' directory and create the inventory file:

bash
Copy code
nano hosts
Add server information in the file.

Generate SSH Keys: On the controller machine, generate an SSH key:

bash
Copy code
ssh-keygen
Copy the public key to the authorized_keys file on each host machine.

Add Servers to Inventory: Open the inventory file and add the server details in INI format:

ini
Copy code
[web_servers]
ipnx@172.31.26.237
ubuntu@172.31.28.157

[db_servers]
ubuntu@172.31.26.249
ubuntu@172.31.26.248
Test Connections: Run the following command to test the connections:

bash
Copy code
ansible all -m ping -i hosts
Write the Ansible Playbook: Open the playbook file and write the tasks for installing Apache2 and MySQL:

yaml
Copy code
---
- name: Play 1
  hosts: web_servers
  become: yes
  tasks:
    - name: Update apt cache
      apt:
        update_cache: yes
    - name: Install Apache2
      apt:
        name: apache2
        state: present
    - name: Ensure Apache2 is running
      service:
        name: apache2
        state: started
        enabled: yes

- name: Play 2
  hosts: db_servers
  become: yes
  tasks:
    - name: Update apt cache
      apt:
        update_cache: yes
    - name: Install MySQL
      apt:
        name: mysql-server
        state: present
    - name: Ensure MySQL is running
      service:
        name: mysql
        state: started
        enabled: yes
Check Syntax and Run Playbook: Verify the playbook syntax:

bash
Copy code
ansible-playbook --syntax-check playbook.yml
If there are no errors, deploy the playbook:

bash
Copy code
ansible-playbook -i hosts playbook.yml
Conclusion

This project guides you through provisioning Apache2 and MySQL on remote servers using Ansible. Follow the steps carefully to ensure successful setup and deployment. Thank you and keep learning!
