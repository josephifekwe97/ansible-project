1.## Overview

This guide details how to configure Ansible on Amazon Linux and Ubuntu systems to manage both Linux and Windows nodes. It includes setting up EC2 instances, configuring secure connections, and writing Ansible playbooks for automation.

2.## Amazon Linux Setup

Step 1: Create EC2 Instances

Launch three EC2 instances:

Controller: Amazon Linux instance to run Ansible.

Linux Node: Amazon Linux instance to be managed.

Windows Node: Windows Server instance to be managed.

Configure security groups:

Open port 22 (SSH) for Linux instances.

Open port 5986 (WinRM-HTTPS) for the Windows instance.

Step 2: SSH into the Controller

ssh -i <your-key.pem> ec2-user@<controller-public-ip>

## Step 3: Install Ansible on the Controller

sudo amazon-linux-extras install ansible2 -y

Step 4: Verify Ansible Installation

ansible --version

Step 5: Install the PyWinRM Module

Install Python and PyWinRM for Windows management:

sudo yum install python3-pip -y
pip3 install --upgrade --force-reinstall pip==9.0.3
pip3 install pywinrm

Step 6: Edit Ansible Inventory File

Navigate to the Ansible directory:

cd /etc/ansible

Edit the hosts file:

sudo vi hosts

Add the following:

[windows]
<windows-private-ip>

[windows:vars]
ansible_user=ansadmin
ansible_password="<strong-password>"
ansible_connection=winrm
ansible_port=5986
ansible_winrm_server_cert_validation=ignore

Step 7: Configure WinRM on Windows Node

Log into the Windows instance and download the ConfigureRemotingForAnsible.ps1 script.

Open PowerShell as Administrator and run:

powershell.exe -ExecutionPolicy Bypass -File .\ConfigureRemotingForAnsible.ps1

Create a user for Ansible:

Open compmgmt.msc.

Navigate to Local Users and Groups > Users.

Create a new user ansadmin and set a strong password.

Add the user to the Administrators group.

Step 8: Test Ansible Connection to Windows

ansible all -m win_ping
ansible all -m win_command -a "whoami.exe"
ansible all -m win_command -a "cmd.exe /c mkdir c:\test"

## 3. Managing Linux Node with Ansible

Step 1: Configure the Linux Node

SSH into the Linux node:

ssh -i <your-key.pem> ec2-user@<linux-node-ip>

Enable password authentication:

sudo vi /etc/ssh/sshd_config

Set PasswordAuthentication to yes.

Restart the SSH service:

sudo systemctl restart sshd

Set a password for the ec2-user:

sudo passwd ec2-user

Step 2: Configure SSH Key-Based Authentication

Generate an SSH key pair on the controller:

ssh-keygen

Copy the public key to the Linux node:

ssh-copy-id ec2-user@<linux-node-ip>

Revert password authentication settings on the Linux node:

sudo vi /etc/ssh/sshd_config

Set PasswordAuthentication to no.

Restart the SSH service:

sudo systemctl restart sshd

Step 3: Add Linux Node to Ansible Inventory

Edit the hosts file:

[linux]
<linux-private-ip>

[linux:vars]
ansible_user=ec2-user
ansible_ssh_private_key_file=~/.ssh/id_rsa

Step 4: Test Connection to Linux Node

ansible -i hosts linux -m ping

## 4. Ubuntu Setup

Step 1: Install Ansible

sudo apt update
sudo apt install ansible -y

Step 2: Create Inventory File

Create inventory.ini:

sudo vi inventory.ini

Add the following:

[webservers]
ubuntu_server ansible_host=<ubuntu-ip> ansible_connection=ssh ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/id_rsa

[windows]
windows_server ansible_host=<windows-ip> ansible_user=Administrator ansible_password=<strong-password> ansible_connection=winrm ansible_winrm_server_cert_validation=ignore

Step 3: Test Connections

ansible -i inventory.ini all -m ping

5.## Writing a Playbook to Install LAMP on Ubuntu

Create a YAML Playbook

Create lamp.yaml:

vi lamp.yaml

Add the following:

- hosts: webservers
  become: yes
  vars:
    mysql_root_password: "<your-password>"
    php_packages:
      - php
      - php-mysql
      - libapache2-mod-php
      - php-cli
      - php-curl
      - php-json
      - php-cgi
      - php-gd
      - php-mbstring
  tasks:
    - name: Ensure Python 3 is installed
      apt:
        name: python3
        state: present

    - name: Install Python MySQL libraries
      apt:
        name: python3-pymysql
        state: present

    - name: Update APT package index
      apt:
        update_cache: yes

    - name: Install Apache
      apt:
        name: apache2
        state: present

    - name: Start and enable Apache
      service:
        name: apache2
        state: started
        enabled: yes

    - name: Install MySQL server
      apt:
        name: mysql-server
        state: present

    - name: Start and enable MySQL service
      service:
        name: mysql
        state: started
        enabled: yes

    - name: Install PHP and modules
      apt:
        name: "{{ php_packages }}"
        state: present

    - name: Configure Apache to use PHP
      copy:
        dest: /var/www/html/info.php
        content: "<?php phpinfo(); ?>"

    - name: Enable Apache mod_rewrite
      command: a2enmod rewrite
      notify:
        - Restart Apache

  handlers:
    - name: Restart Apache
      service:
        name: apache2
        state: restarted

Run the Playbook

ansible-playbook -i inventory.ini lamp.yaml

 ## 6. Validation

Test Windows connection:

ansible all -m win_ping

Test Linux connection:

ansible -i inventory.ini linux -m ping

Access the web server:
Open http://<ubuntu-server-ip>/info.php in a browser.

## 7. Conclusion

By following this guide, you can effectively manage both Linux and Windows nodes using Ansible. The steps provide a foundation for automating complex configurations and deployments.

