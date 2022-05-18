### Infrastructre as Code (IaC)

# What is IaC?
- managing and provisioning of infrastructure through code instead of through manual processes. With IaC, configuration files are created that contain your infrastructure specifications, which makes it easier to edit and distribute configurations. Automating infrastructure provisioning with IaC means that developers don’t need to manually provision and manage servers, operating systems, storage, and other infrastructure components each time they develop or deploy an application.

# What are the benefits?
- With cloud computing, the number of infrastructure components has grown, more applications are being released to production on a daily basis, and infrastructure needs to be able to be spun up, scaled, and taken down frequently. Without an IaC practice in place, it becomes increasingly difficult to manage the scale of today’s infrastructure.
- IaC can help your organization manage IT infrastructure needs while also improving consistency and reducing errors and manual configuration
- Benefits:
  - Cost reduction
  - Increase in speed of deployments
  - Reduce errors
  - Improve infrastructure consistency
  - Eliminate configuration drift
  - IaC tool examples

# What tools are available?
- Chef
- Puppet
- Red Hat Ansible Automation Platform
- Saltstack
- Terraform
- AWS CloudFormation

# What is configuration management and Orchestration under IaC
- Tasks are generally divided into the domains of configuration management and configuration orchestration.
  - Configuration management
    - Generally, Ansible, Puppet, SaltStack, and Chef are considered to be configuration management (CM) tools and were created to install and manage software on existing server instances (e.g., installation of packages, starting of services, installing scripts or config files on the instance). They do the heavy lifting of making one or many instances perform their roles without the user needing to specify the exact commands. No more manual configuration or ad-hoc scripts are needed.
  - Configuration orchestration
    - Tools like Terraform are considered to be orchestrators. They are designed to provision the server instances themselves, leaving the job of configuring those servers to other tools. Orchestration addresses the requirement to provision environments at a higher level than configuration management. The focus here is on coordinating configuration across complex environments and clusters.

# What tools are available for config management and orchestration
    - Config management
      - Ansible, Puppet, Saltstack, Chef
    - Config orchestration
      - Terraform, Cloud Formation (aws)

    - Ansible - automation tool
      - simple to use, agentless (other server instances which it communicates with do not need Ansible installed), 

# What is the diff between them?
    - Chef and Ansible use a procedural style language where you write code that specifies, step-by-step, how to achieve the desired end state. The onus is on the user to determine the optimal deployment process. Procedural languages are more familiar to system admins who have backgrounds in scripting. 
    - Terraform, SaltStack, and Puppet use a declarative style where you write code that specifies the desired end state. The IaC tool itself then determines how to achieve that state in the most efficient way possible. Declarative tools are more familiar to users with a programming background.

# Why should we use it?
- IaC is an important part of implementing DevOps practices and continuous integration/continuous delivery (CI/CD). IaC takes away the majority of provisioning work from developers, who can execute a script to have their infrastructure ready to go. 
- That way, application deployments aren’t held up waiting for the infrastructure, and sysadmins aren’t managing time-consuming manual processes.  
- In order for an environment to be automated it needs to be consistent. Automating application deployments doesn’t work when the development team deploys applications or configures environments one way and the operations teams deploys and configures another way.
- IaC helps you to align development and operations because both teams can use the same description of the application deployment, supporting a DevOps approach.
- The same deployment process should be used for every environment, including your production environment. IaC generates the same environment every time it is used.

# Mutable or immutable?
- Traditional server environments are mutable, in that they are changed after they are installed. Administrators are always making tweaks or adding code. CM tools evolved to manage this complexity and bring order to the configuration and updating of tens to thousands of servers.
- An immutable infrastructure is one in which servers are never modified after they’re deployed. If something needs to be updated or changed, new servers are built afresh from a common template with the desired changes.

# use cases and examples#
- software development
- infrastructure management
- cloud monitoring

# Ansible
- Ansible is an IT automation tool. It can configure systems, deploy software, and orchestrate more advanced IT tasks such as continuous deployments or zero downtime rolling updates.
- Ansible manages machines in an agent-less manner. There is never a question of how to upgrade remote daemons or the problem of not being able to manage systems because daemons are uninstalled. Because OpenSSH is one of the most peer-reviewed open source components, security exposure is greatly reduced. Ansible is decentralized–it relies on your existing OS credentials to control access to remote machines.

# Exercise
- Create 2/3 VMs with vagrant
- create Ansible controller with 2 agent nodes - web & db
- Set up Ansible controller with required dependencies
- test Ansible --version
- python --version 2.7 - ideally 3 or above
- What is YAML/YML file.yml file.yaml
- Adhoc commands with Ansible
- testing connection between Controller and agent nodes
- ssh - vagrant - between VMs
- Ansible Folder/file structure
- Ansible Hosts/Inventory
- Ansible playbooks - playbook.yml
- Why learn YML - used with Ansible - Cloud Formation AWS - Docker compose
- Kubernetes

# Process for setting up 3 instances with Ansible (controller, web, db) using Vagrant
- copy app directory to remote web instance `scp -r ~/Downloads/starter-code/ vagrant@192.168.33.10:/home/vagrant`
- ssh into each and install dependencies one at a time: `vagrant ssh controller` - `vagrant ssh web` - `vagrant ssh db`
- `sudo apt-get update -y`
- `sudo apt-get upgrade -y`
- python dependencies
  - `sudo apt-get install software-properties-common` 
- Add python's repo
  - `sudo apt-add-repository ppa:ansible/ansible`
- Install Ansible
  - `sudo apt-get install ansible`
  - `ansible --version`
- Now check for connection between controller and agent nodes
  - `ssh vagrant@192.168.33.11` (db ip)
  - password: vagrant
  - `sudo apt-get update -y`    
  - `ssh vagrant@192.168.33.10` (web ip)
  - `sudo apt-get update -y`
- Let Ansible know there are two nodes
  - `cd /etc/ansible`
  - `ls`
  - optional install tool => `sudo apt-get install tree -y`
  - `tree`
  - In the 'hosts' file we need to enter the two nodes
    - `sudo nano hosts`
    - add the the two nodes in the 'hosts' file in 'example 1' in the following format:
        ```
        [web]
        192.168.33.10 ansible_connection=ssh ansible_ssh_user=vagrant ansible_ssh_pass=vagrant
        [db]
        192.168.33.11 ansible_connection=ssh ansible_ssh_user=vagrant ansible_ssh_pass=vagrant
        ```
      - `cat hosts` to confirm it's saved
      - `ansible web -m ping` (ping from controller to web instance)
      - `ansible db -m ping` (ping from controller to db instance)
  
- For additional machines not created by us, find out more information about them with 'adhoc' commands
  - `ansible web -a "uname -a" (for single machine)
  - `ansible all -a "uname -a" (for all machines)
  - copy file from one to another
    - `ansible web -m copy -a 'src=/etc/ansible/test.txt dest=/home/vagrant'`

- We'll need to check space availability, permissions, OS, python versions - required dependencies, network, memory, disk space
  - Create script full of adhoc commands to automate process (playbooks)
    - `sudo nano nginx-playbook.yml`
    - Once this file opens, configure as follows:

    ```
    # creating a playbook to configure nginx web server in web machine
    # YAML YML file starts three dashes ---
    ---
    # where do we want to install nginx
      - hosts: web
    # do we want to see logs - gather facts
      gather_facts: yes
    # what permissions do we need to run this playbook
      become: true
    # what is the name of the package
      tasks:
       - name: install Nginx
         apt: pkg=nginx state=present
    ```
- find the status of nginx once the playbook is run
  - `sudo ansible-playbook nginx-playbook.yml`