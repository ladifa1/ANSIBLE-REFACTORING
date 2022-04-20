ANSIBLE REFACTORING AND STATIC ASSIGNMENTS

Create an "ansible-config-artifact" directory on jenkins-ansible sever used on project 11

`sudo mkdir /home/ubuntu/ansible-config-artifact`

![](images/1.png)

Give jenkins user permissions to the folder

`sudo setfacl -m u:jenkins:rwx /home`

`sudo setfacl -m u:jenkins:rwx /home/ubuntu/ansible-config-artifact`

On Jenkins web console, install copy artifact plugin

Create and configure a new Freestyle project "save-artifacts" 

![](images/2.png)

![](images/3.png)

Test configuration by making a change in the ansible-config-mgt repo 

![](images/4.png)

![](images/7.png)

Check jenkins to confirm if build was successful

![](images/5.png)

![](images/6.png)

## Refactor Ansible code by importing other playbooks into site.yml

In ansible-config-mgt repository used in project 11

Create a new branch "refactor" on ansible-config-mgt repo

Create a file "sites.yml" within the playbooks folder

Create a new folder in root of the repository and name it static-assignments

Move common.yml file into static-assignments folder

![](images/git.png)

In the previous project (project 11) wireshark was installed with commmon.yml playbook.

Create a new playbook common-del.yml under static-assignments add below code to delete wireshark from all servers

![](images/del.png)

Update sites.yml file with import code

![](images/sites.png)

Edit ansible.cfg file to point inventory file to /home/ubuntu/ansible-config-artifact/inventory 

`sudo vi /etc/ansible/ansible.cfg`

![](images/8.png)

Change directory to ansible-config-artifact

`cd /home/ubuntu/ansible-config-artifact`

Run ansible ping

`ansible all -m ping`

![](images/9.png)

Run playbook

`ansible-playbook -i /home/ubuntu/ansible-config-artifact/inventory/dev.yml /home/ubuntu/ansible-config-artifact/playbooks/sites.yml`

![](images/10.png)

![](images/11.png)

![](images/12.png)

Check servers to confirm wireshark was deleted

![](images/13.png)

## CONFIGURE UAT WEBSERVERS WITH A ROLE ‘WEBSERVER’

Create 2 new red-hat EC2 instances to serve as UAT servers 

open http port in inbound rules

Create the below directory/files structure manually

![](images/14.png)

Update inventory with UAT servers IP addresses

![](images/15.png)

Edit /etc/ansible/ansible.cfg, change role path to /home/ubuntu/ansible-config-artifact/roles

`sudo vi /etc/ansible/ansible.cfg`

In task directory under webservers add the below code to main.yml playbook to:

1. Install and configure Apache
2. Clone Tooling website from GitHub https://github.com/ladifa1/tooling.git
3. Ensure the tooling website code is deployed to /var/www/html on each of 2 UAT Web servers
4. Make sure httpd service is started

![](images/16.png)

In static-assignments folder, create a new assignment uat-webservers.yml

![](images/uat.png)

Since the entry point is the sites.yml file,  refer uat-webservers.yml role inside site.yml

![](images/17.png)

Commit your changes, create a Pull Request and merge them to main branch

![](images/18.png)

Check jenkins to confirm new build 

![](images/19.png)

![](images/20.png)

Run playbook against UAT inventory

`ansible-playbook -i /home/ubuntu/ansible-config-artifact/inventory/uat.yml /home/ubuntu/ansible-config-artifact/playbooks/site.yml`

![](images/21.png)

Verfiy both UAT webservers are configured via browser.

Use public ip to access tooling site

![](images/22.png)

![](images/23.png)