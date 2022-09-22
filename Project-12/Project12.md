
# ANSIBLE REFACTORING AND STATIC ASSIGNMENTS (IMPORTS AND ROLES)

In this project I continued working with my ansible-config-mgt repository and made some improvements to my code.
I refactored the Ansible code, created assignments, and used the imports functionality. Imports allow to effectively re-use previously created playbooks in a new playbook – it allows you to organize your tasks and reuse them when needed.
Jenkins job enhancement.

New change in the codes creates a separate directory which is not very convenient when we want to run some commands from one place. This consumes space on Jenkins serves with each subsequent change.

- Create a new directory called ansible-config-artifact – all artifacts will be stored here after each build.

 `sudo mkdir /home/ubuntu/ansible-config-artifact`

- Change permissions to this directory, so Jenkins could save files there:

 `sudo chmod -R 0777 /home/ubuntu/ansible-config-artifact`

- Go to Jenkins web console -> Manage Jenkins -> Manage Plugins -> and on Available tab searched for Copy Artifact and installed this plugin without restarting Jenkins

![alt text](./Images/pic%201.jpg)

- Create a new Freestyle project and name it save_artifacts. This project will be triggered by completion of the existing ansible project. 

![alt text](./Images/pic%202.jpg)

![alt text](./Images/pic%203.jpg)

![alt text](./Images/pic%204.jpg)

- The main idea of save_artifacts project is to **save artifacts** into **/home/ubuntu/ansible-config-artifact** directory. To achieve this, I created a Build step and chose Copy artifacts from other project, specified ansible as a source project and /home/ubuntu/ansible-config-artifact as a target directory.

![alt text](./Images/pic%205.jpg)

# Refactor Ansible Code By Importing Other Playbooks Into site.yml

- From the VScode editer, within playbooks folder,create a new file and name it **site.yml** – This file will now be considered as an entry point into the entire infrastructure configuration. Other playbooks will be included here as a reference, site.yml will become a parent to all other playbooks that will be developed.

- Create a new folder in root of the repository and named it static-assignments. The static-assignments folder is where all other children playbooks will be stored. This is merely for easy organization of my work.

- Move common.yml file into the newly created static-assignments folder.

Inside site.yml file, import common.yml playbook using the code below:

 `---
 - hosts: all
 - import_playbook: ../static-assignments/common.yml`

This what the folder structure looks like:

![alt text](./Images/pic%208.jpg)

- Create another playbook under static-assignments and name it **common-del.yml**. In this playbook, configure deletion of wireshark utility.

![alt text](./Images/pic%209.jpg)

In /etc/ansible/ansible.cfg file uncomment inventory string and provided a full path to the inventory directory = /home/ubuntu/ansible/ansible-config-artifact/inventory, so Ansible could know where to find configured roles.

 `sudo vi /etc/ansible/ansible.cfg`

![alt text](./Images/pic%206.jpg)

![alt text](./Images/pic%207.jpg)

- Execute the command below

 `ansible-playbook -i inventory/dev.yml playbooks/site.yml`

 ![alt text](./Images/pic%2010.jpg)


 # CONFIGURE UAT WEBSERVERS WITH A ROLE ‘WEBSERVER’

 - Launch 2 fresh EC2 instances using RHEL 8 image, for our uat servers

 - Create a role by first creating a directory called roles/, within my ansible_config_mgt directory. The role directory must be relative to the playbook file or in /etc/ansible/ directory.

- The roles structure should look like this.

![alt text](./Images/pic%2011.jpg)

- Update the inventory ansible-config/inventory/uat.yml file with IP addresses of the 2 UAT Webservers.

![alt text](./Images/pic%2012.jpg)

- In /etc/ansible/ansible.cfg file uncomment the roles_path string and provide the full path to the roles directory roles_path = /home/ubuntu/ansible-config-mgt/roles, so Ansible will know where to find configured roles.

 `sudo vi /etc/ansible/ansible.cfg`

![alt text](./Images/pic%2013.jpg)

- Go into tasks directory, and within the main.yml file, start writing configuration tasks to do the following:

1. Install and configure Apache (httpd service)
2. Clone Tooling website from GitHub https://github.com/Mariam-Lawal5/tooling.git.
3. Ensure the tooling website code is deployed to /var/www/html on each of 2 UAT Web servers
4. Make sure httpd service is started

![alt text](./Images/pic%2014.jpg)


# REFERENCE WEBSERVER ROLE

- Within the static-assignments folder, create a new assignment for uat-webservers **uat-webservers.yml**. This is where you will reference the role.

![alt text](./Images/pic%2015.jpg)

- Remember that the entry point to our ansible configuration is the site.yml file therefore , we need to refer uat-webservers.yml role inside site.yml. The site.yml will look like:

![alt text](./Images/pic%2016.jpg)

# Commit & Test

- Commit your changes, create a Pull Request and merge them to master (main) branch, make sure webhook triggered two consequent Jenkins jobs, they ran successfully and copied all the files to your Jenkins-Ansible server into /home/ubuntu/ansible-config-mgt/ directory.

Now run the playbook against the uat inventory.

 `ansible-playbook -i inventory/uat.yml playbooks/site.yml`

![alt text](./Images/pic%2017.jpg)

- You should be able to see both of the UAT Web servers configured and you can try to reach them from your browser: 

 `http://<Web1-UAT-Server-Public-IP-or-Public-DNS-Name>/index.php`

The Ansible architecture now looks like this:

![alt text](./Images/pic%2018.jpg)
