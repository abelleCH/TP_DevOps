# TP Part 03 Ansible - Abelle Charlemagne
## Target Application
Install and deploy your application automatically with ansible.
## Introduction
### 3-1 Document your inventory and base commands;

For the connection :
1) Open a WSL terminal and download ansible with ``sudo apt install ansible``
2) Put the key in the ./ssh
3) Set up access with ``chmod 400 .ssh/id_rsa``
4) ``ssh -i .ssh/id_rsa centos@abelle.charlemagne.takima.cloud``

For the ping
1) Change the folder stup.yml with the hostname and path to key (See code below)
3) Access to ``/mnt/c/Users/Admin/OneDrive/Documents/Bureau/TP_devops/TP1_devops/ansible``
4) ``ansible all -i inventories/setup.yml -m setup -a "filter=ansible_distribution*"``

In the inventory we have the file setup.yml :
```yaml
all:
 vars:
   ansible_user: centos
   ansible_ssh_private_key_file: ~/.ssh/id_rsa
 children:
   prod:
     hosts: abelle.charlemagne.takima.cloud 
```


### 3-2 Document your playbook
In the playbook file we finally just have :

```yaml
- hosts: all
  gather_facts: false
  become: true

  roles:
    - docker
```
That calls the docker roles and in this directory we have 
- in tasks :
````yaml
# tasks file for roles/docker
  - name: Install device-mapper-persistent-data
    yum:
      name: device-mapper-persistent-data
      state: latest

  - name: Install lvm2
    yum:
      name: lvm2
      state: latest

  - name: add repo docker
    command:
      cmd: sudo yum-config-manager --add-repo=https://download.docker.com/linux/centos/docker-ce.repo

  - name: Install Docker
    yum:
      name: docker-ce
      state: present

  - name: Install python3
    yum:
      name: python3
      state: present

  - name: Install docker with Python 3
    pip:
      name: docker
      executable: pip3
    vars:
      ansible_python_interpreter: /usr/bin/python3

  - name: Make sure Docker is running
    service: name=docker state=started
    tags: docker
````


## Deploy your App

We have 5 roles : 
- docker
- network
- database
- app
- proxy