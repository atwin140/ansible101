# Ansible 101
Ansible is a force multiplier that can allow you to work on many machines at a time. 

## Many Servers
Everyday admin can easily to change one server ok, manage 10, now you can save a ton of time with Ansible. 

Repeatable! Every time you run your playbook the built in logic checks and only runs what it has to.

## Automation without the programing
You do not need to worry about complex algorithms to find that one word you need to change but use the built in logic to accomplish the find and replace.
With Ansible you declare the state you want and 
that out how to do something but you declare the state you want.




## Agentless
No limited to just linux

Ansible model is a push. Changes are not applied until you send the commands

## Get Started
- Make sure you can ssh into the Ansible-Master EC2 instance
  1. check Ansible version  - Command:   
  `ansible --version`
  1. Check Ansible config file - Command  
  `vi ansible.cfg`  
  1. Update the inventory file line with  
  `inventory = inventory.ini`  
      * Note: There are many ways to use the ansible.cfg file but we will implement it in the directory we are working in.
  1. Test ansible with the ping module  
  `ansible all -m ping`
  

---  
## Ad-hoc Commands:
Ansible ad-hoc commands are great for a one off check and they can easily be turned in to a playbook for repeat use. The Syntax is a little different than most examples but still can be used in a playbook if not a little harder to read.  
-  The Basic format:  
  `ansible [targets] -m [module] -a [attributes]`  
  `ansible all -m shell -a "uptime"`
    * Note: we want to avoid using the shell command as much as we can. Shell commands will always run.

### Example usage: Update a system
We always have to update systems and using Ansible for this is easy. Later we will use a playbook but for now we are going to us an ad-hoc command.  
  - `ansible all -m yum -a "pkg=* state=latest"`  

This will connect to all servers in the inventory and run the "yum" module. We want all the packages updated to a state of latest. We can replace the "pkg=*" with a program name we want to install.
- `ansible all -m yum -a "pkg=firewalld state=latest"`  

### Example usage: Enable a service
We can use ansible to accomplish anything you can do from a command line. Shell will allow this but using the modules allows for a better idempotent state. If a command does not have to run it will not if the modules are used.  
  * example with shell
  - `ansible all -m shell -a "systemctl enable firewalld"`

  * example with module
  - `ansible all -m systemd -a "name=firewalld state=started enabled=yes"`

---
## Playbooks
The real power of configuration as code comes in with the playbook.  
This is a YAML file that declares how you want the target to be configured.  
```yaml
---
- hosts: all
  vars:
    myName: "UserName"

  tasks:
```  
   - With Yaml it is very important that you use spaces not tabs. the standard is two spaces for each indent.  
### There are three key parts to the playbook here
  1. `- hosts: all` declares the targets we are going to work with
    - Examples:
      - `- hosts: web`
      - `- hosts: httpd`
  1. `  vars:`  This is where you can declare variables to be used in your playbook, there are many other you can use that we will talk about later.
  1. `tasks:` This is where we start adding what we want to change.
  * Note: you can also have `roles` but that is outside of the scope of this class.

### Tasks 
  Here we will add a few tasks and introduce some fun modules.  
  1. File  
    1. The file module is used to manage files and file properties  
      * Set permissions  (mode: 0644 )
      * Create or remove files (state: touch)  
  - Example: This will create a file with the permissions of 0644  
    ```
    - name: This name is printed when the task runs
    file:
        path: /etc/profile.d/proxy.sh
        state: touch
        mode: '0644'
    ```  

  2. lineinfile  
    1. This module manages lines in a text file  
      * searched for text in a line and replaces the line  
  - Example:   
    ```
    - name: Add proxy export line to default profile
    lineinfile:
        path: /etc/profile.d/proxy.sh
        regexp: '^export https_'
        line: 'export https_proxy=proxy'
        state: present
    ```  
  3. Yum  
    1. The yum module is used to manage packages with the yum module  
      * Install / remove packages
      * Update packages  
  - Example:   
    ```
    - name: Install httpd
    yum:
        pkg: httpd
        state: latest
    ```  


Homework
create a new server in AWS  
  - Use the RHEL 8 AMI
  - Use the Ansible SSH Security Group
  - Use the Ansible key

Add the Ansible.pem file to you ssh keys
  - copy the pem file to the linux server
  - ``` eval `ssh-agent`; ssh-add Ansible.pem```    

SSH to the Ansible master as the ansible user   
  - `ssh ansible@1232131`  

Create a new folder with your NTID  
  - `mkdir (your NTID)`  

Change to the new directory and git clone the repo
  - `cd (NTID)`
  - `git clone ocp`

Add your new host ip address to the inventory (you can get the ip from the AWS console)
  - `vi inventory.ini`

Test your new server  
 - `ansible all -m ping`  


Write a playbook to:
  - Update all the servers
  - Install httpd on your server
  - Add your name and a timestamp to /root/helloworld on t1


























