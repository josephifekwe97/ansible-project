<!-- This is going to be a simple project where we are going to provission apaech2 on two remote server and mysql-server on two remote server using ansible  -->

<!-- My name is Rubben, and am going to be your intructor on this project. So let begin -->

<!-- ANSIBLE PROJECT

WHAT IS ANSIBLE?
    Ansible is an open-source automation tool that can be use to provision infrastructure on one or more computer systems which can be in the cloud, on-prem or hybride(partly on prem and in cloud). 
    Some of the tasks we can do with ansible include configuration management, application deployment, infrastructure set up etc..

SETTING UP THE CONTROLLER:
    Ansible Controller is the machine or server where we set up ansible to run ansible-command and playbooks, this is where we manage configurations and deployment of application to other hosts.
    The controller can be any machine that meet the requirment for uning ansible such as a laptop, a desktop, a virtue machine or cloud-base instance(EC2)
    On this controller, we are going to create Two files called ANSIBLE INVENTORY AND ANSIBLE PLAYBOOK.

   ANSIBLE INVENTORY: This is the file where we will keep the information about our servers(hosts) in a structural manner like the ip addresses or the domain names, how we are going to connect to them 
        etc. The Inventory file is writen in INI which is just like a text. Also, we use the concept of groups to group servers according to the kind of job we want to carry out on them. 
        example of an Inventiry file is shown bellow

        [database_servers]
        Ipnx@172.31.28.157
        Ubuntu@172.31.28.157
        we_app@172.31.26.237

        [application_servers]
        Ipnx@172.31.28.157
        Ubuntu@172.31.28.157
        we_app@172.31.26.237
        
        Here, we grouped our servers according the services we have running on them

   ANSIBLE PLAYBOOK:
        This is the file where we will write the tasks that we want Ansible to run/perform on the host machines/servers and this file is writen in YAML formate. 
        yaml language is used for analyzing data and it mainly made up of key value pairs(dictionary) and lists

SETTING UP THE HOSTS:
    Ansible is agentless, meaning we do not need to set up anything like a client app on the host machines other than some form of connection to the controller machine 
    and we will be using ssh connection in this project.

So lets begin

step 1.
    we are going to spin up 5 instances on AWS using one as a controller and the other four as the hosts machines.
    On the server we are using as our controller, we are going to install Ansible.

step 2.
   On this controller server, we will then create a folder, we can call it anything but for the sake of this demo, we call it 'ansible-project' and inside this foler, we are going to have two files one as the inventory file
   and the other as the playbook file. The playbook file is writen in yaml. To create this directory/folder, we run the command 'mkdir ansible-project'

step 3.
    We then install ansible on the controller machine by running the following commands:
                $ sudo apt update
                $ sudo apt install software-properties-common
                $ sudo add-apt-repository --yes --update ppa:ansible/ansible
                $ sudo apt install ansible
    after which we check if ansible was properly installed by running 
                $ sudo ansible --version
step 4.
    We then navigate into "ansible-project" and create our INVENTORY FILE, we can call this file anything so let call it hosts
    so we just run this command "sudo touch hosts" to create the file and inside this file is where we are going to store our inventories.

step 6.
    Still on that directory(ansible-project), we will create another file and this file will be our ANSIBLE PLAYBOOK and we will call it PLAYBOOK.YML. 
    So we just run the command "sudo touch playbook.yml" to create the file.

step 7.
    On the controller machine, we run the command "ssh-keygen" on the current user on the controller server to generate the pub_key and then go into the folder '.ssh' where the keys are stored and then cat the pub_key by running "cat id_rsa.pub" 
    and copy and past this key on the authorized_keys of the host machines by connecting to each of the host machines and then cd into the .ssh directory and nano/vi into the authorized_keys and then past
    the pub.key from the controller there then save and close and then try to ssh from the controller to any of the host machines and if this done properly, we should be able to move from the controller to 
    any of the hosts machines.

step 8.
    And if that is successful, We then go back to the controller server and cd into the ansible-project directory/folder and open the HOSTS file to add the servers into the files using INI formate. 
    So we will be using NANO as the text editor to open the file. Below is the sample of how the file is going to look like:

                [web_servers]
                ipx@172.31.26.237
                ubuntu@172.31.28.157

                [db_servers]
                ubuntu@172.31.26.249
                ubuntu@172.31.26.248
   after adding the servers ips or domain names we then save close and exit the file.

   Then cd into the ansible-project directory/foler and then run the command to test the connections since we have included the ips of the servers in the hosts file. 
   So we run "ansible all -m ping -i hosts" if you do this well, you should get a success message, ping pung

step 9.
    Now that our controller can communicate/ping the host machines, we can now write our ansible playbook where we will tell ansible to install apache2 and mysql_server on all the host machines according to 
    the groupings and start them. So again we navigate to the ansible-project folder and use our nano editor to go into the playbook file where we can now write the tasks we want ansible to perform on the hosts machines

step 10.
    Basically, playbooks are writen in yaml langusge and like I said before yaml is mainly made up of dictionary and list, so the play is in dictionery and the tasks are in lists. 
    So since we are to install apache2 on two servers and mysql on the other two, the task is going to be broken into three stage for reach of the groups, first is to update the vm, 
    second is to install the apache2 via apt since we are using ubuntu and the last stage is to start the service and same goes for mysql installation so the playbook should look like this

    ---
- name: Play 1
  hosts: web_servers
  become: yes

  tasks:
    - name: update apt cache
      apt:
        update_cache: yes

    - name: install apache2
      apt:
        name: apache2
        state: present

    - name: Ensure apache2 service is running
      service:
        name: apache2
        state: started
        enabled: yes

- name: Play 2
  hosts: db_servers
  become: yes

  tasks:
    - name: update apt cache
      apt:
        update_cache: yes

    - name: install mysql
      apt:
        name: mysql-server
        state: present

    - name: Ensure mysql service is running
      service:
        name: mysql
        state: started
        enabled: yes

You can use the above playbook and edit the servers you want to run apache2 and mysql on.

step 11.
    after using crl x then y then enter to save and close the nano editor, you can now run the playbook using the command "ansible-playbook --syntax-check playbook.yaml" to check if there is any error in
    the code, and if there are none,it should return the playbbok name and maybe some warnings. then we can proceed or if there be any error we then have to troublr shoot them from the playbook. 

step 12. And if there be no errors in our code, we can then go ahead and deploy the playbook by running the ommand "ansible-playbook -i hosts playbook.yaml" then seat back and have ansible do the 
    installation of apache2 and mysql-server on those hosts follwing the plays on the playbook and after which we can go into any of those server to confirm if you are in doubt. NB: ansible will give you a recap of the process which will 
    contain the failed insytallation if there be any and the successfull installation......


THANKS AND NEVER STOP LEARNING....
