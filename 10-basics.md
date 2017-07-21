# Ansible Documentation

## Getting Started
---
First things first. We need to get Ansible up and running on your local machine. There are a couple of options. You can install via PIP(Python package manager) or via apt/yum if you're on linux. *Note* : *If you want the latest version of Ansible, you must use pip.*

    sudo pip install ansible
    apt install ansible
    yum install ansible

Now that we have ansible installed, you can do a test run on your local machine.

    ansible localhost -m setup

This should output all the details ansible can scrape from your machine. If you'd like more information on what each of the parameters mean in ansible you can read through the man pages

    man ansible
    man ansible-playbook

## Basics
---
Now that we have ansible installed and working. Let's build a hosts file, copy our ssh keys and run some tests

### What is a hosts file?
---
A hosts file, plain and simple is an inventory of all your hosts. You can place each of your hosts in groups and run ansible playbooks or commands against those groups so that they run on every system. A basic hosts file will look like this:

    [development]
    dev-web01.sys.inf
    dev-web02.sys.inf
    mysql01.sys.inf

    [mysql-production]
    mysql01.sys.inf

    [web-production]
    web01.sys.inf
    web02.sys.inf

*http://docs.ansible.com/ansible/latest/intro_inventory.html*

As you can see it's pretty simple. There are some other things you can define as well as the hosts. You can define what user will be used to login on each host, what password, there is more information on this in ansible's documentation.

Hosts can live in multiple groups, think of a hosts file as just a way to organize your hosts

So lets create a basic hosts file to run against our test hosts. It may be wise to create an "ansible" directory for storage of all your files while working.

    aloukinas@commander:[~]: vim hosts

    [dev]
    192.168.1.50
    192.168.1.51
    192.168.1.52

*Change ip addresses or hostnames based on your development envrionment*

### Lets copy our ssh-keys to each host
---
Most likely you don't already have keys setup on your machine. You can do this by:

    aloukinas@commander:[~]: ssh-keygen -t rsa -b 4096

    Generating public/private rsa key pair.
    Enter a file in which to save the key (/home/user/.ssh/id_rsa):[Press enter]
    Enter passphrase (empty for no passphrase): [Type a passphrase]
    Enter same passphrase again: [Type passphrase again]

*You can leave the passphrase blank in most cases*

Now that you have a private and public key, we can copy our public key to any machine we want to login to without passing a password in.

    aloukinas@commander:[~]: ssh-copy-id username@hostname

Run this against all three hosts and you should be able to ssh in on the username you copied your key to without passing in a password.

### Let's run an ad-hoc command
---
With ssh-keys copied to each machine, we can now have ansible run against them and see if they are alive and well

The most basic ansible command is ping. This checks to see if you can login, and if the hosts is alive.

    aloukinas@commander:[~]: ansible dev -i hosts -u username -m ping

We've added some parameters that are new.. That's okay we are going to cover them.

The "dev" after ansible is the host group that we are running our ansible commands against. This is the only hard requirement to run an ansible command.

The -i parameter allows you to specifiy your own hosts file. If you do not specifiy a hosts file, ansible will use the "Default" host file which is typically stored in /etc/ansible/hosts

The -u parameter specifies the user to ssh on. 

The -m is specifiying which module you'd like to use.. This is only really used in ad-hoc commands, in playbooks you have will define that for each task.

*http://docs.ansible.com/ansible/latest/modules_by_category.html*

### Let's setup a basic playbook
---
I'm going to define a playbook in it's most basic form and will link some examples of much larger playbooks with roles for you to take a look at. You could if you really wanted to, create an ansible "play-book" as one large ansible file, but you get the most functionality by segmenting all your tasks to seperate files and variables.

    ---
    - hosts: dev
      remote_user: username
      tasks:
        - name: Ping the server
          ping:

All ansible files are in the .YAML format. This format is EXTREMELY specific on how you layout your files. You must use very specific defined spacing or ansible will complain. Typically it's two spaces on every segment.

Run the playbook! *note: We've already defined the remote_user, and the host group to run this play on.. So we do not need to specifiy that in our command line*

    aloukinas@commander:[~]: ansible-playbook playbook.yml -i hosts

*note: We are still going to define the hosts file to use, we could have done this in the playbook, but this is just a small example*

### Wrapping up
---
That is a basic implementation of ansible and how to get it running. Ansible is massively configurable. Look into the documentation and check ansible-galaxy

### Resources
---
Ansible documentation - 
http://docs.ansible.com/ansible/latest/index.html

Ansible playbooks (roles) - 
https://galaxy.ansible.com/list#/roles?page=1&page_size=10

