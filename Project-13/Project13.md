# ANSIBLE DYNAMIC ASSIGNMENTS (INCLUDE) AND COMMUNITY ROLES

Introducing dynamic assignment into our structure:

We will be continuing from our last project (project 12). In our Github repository start a new branch and call it dynamic-assignments.

 `git checkout -b dynamic-assignments`
 
Create a new folder, name it dynamic-assignments, inside the folder create a file and name it env-vars.yml. Since we will be using the same Ansible to configure multiple environments, and each of these environments will have certain unique attributes, such as servername, ip-address etc., we will need a way to set values to variables per specific environment.

- For this reason, we will now create a folder to keep each environment’s variables file. Therefore, create a new folder env-vars, then for each environment, create new YAML files which we will use to set variables.

Our layout should now look like this

![alt text](./Images/pic%201.jpg)

Paste the instruction below into the env-vars.yml file.

![alt text](./Images/pic%202.jpg)
Notice 3 things to note here:

We used include_vars syntax instead of include, this is because Ansible developers decided to separate different features of the module.

From Ansible version 2.8, the include module is deprecated and variants of include_* must be used. These are:

include_role
include_tasks
include_vars
In the same version, variants of import were also introduces, such as:

import_role
import_tasks
We made use of special variables {{ playbook_dir }} and {{ inventory_file }}. {{ playbook_dir }} will help Ansible to determine the location of the running playbook, and from there navigate to other path on the filesystem.

{{ inventory_file }} on the other hand will dynamically resolve to the name of the inventory file being used, then append .yml so that it picks up the required file within the env-vars folder.

- We are including the variables using a loop. with_first_found implies that, looping through the list of files, the first one found is used. This is good so that we can always set default values in case an environment specific env file does not exist.

- Update site.yml file to use of the dynamic assignment (At this point, we cannot test it yet). 

![alt text](./Images/pic%204.jpg)

- Now it is time to create a role for MySQL database - it should install the MySQL package, create a database and configure users.

But why should we re-invent the wheel? There are tons of roles that have already been developed by other open source engineers out there.

These roles are actually production ready, and dynamic to accomodate most of Linux flavours.

With Ansible Galaxy again, we can simply download a ready to use ansible role, and keep going.

# Download Mysql Ansible Role.

You can browse available community roles here

We will be using a MySQL role developed by geerlingguy.

- On Jenkins-Ansible server make sure that git is installed with git --version, then go to ‘ansible-config-mgt’ directory and run.

- Inside roles directory create your new MySQL role with `ansible-galaxy install geerlingguy.mysql` and rename the folder to mysql using

 `mv geerlingguy.mysql/ mysql`

Read README.md file, and edit roles configuration to use correct credentials for MySQL required for the tooling website.

Roles -> MYSQL -> Defaults -> Main.yml

![alt text](./Images/pic%203.jpg)

# Load Balancer Roles.

- We want to be able to choose which Load Balancer to use, Nginx or Apache, so we need to have two roles respectively:

- Nginx
- Apache

- But because a web server can only make use of one load balancer, the playbook is configured with the use of conditionals- when statement, to ensure that only the desired load balancer role tasks gets to run on the webserver

- Install the Nginx Ansible Role and rename the folder to nginx

 `ansible-galaxy install geerlingguy.nginx`

 `mv geerlingguy.nginx/ nginx`

- Install the Apache Ansible Role and rename the folder to apache

 `ansible-galaxy install geerlingguy.apache`

 `mv geerlingguy.apache/ apache`

- Since we cannot use both Nginx and Apache load balancer at the same time, we need to add a condition to enable either one - this is where we can make use of variables.

- Declare a variable in defaults/main.yml file inside the Nginx and Apache roles. Name each variables enable_nginx_lb and enable_apache_lb respectively.

Set both values to false like this enable_nginx_lb: false and enable_apache_lb: false.

Declare another variable in both roles load_balancer_is_required and set its value to false as well

![alt text](./Images/pic%205.jpg)

- Update both assignment and site.yml files respectively

- loadbalancers.yml file is going to look like this (create this file in your static-assignments folder)
 `
 - hosts: lb
  roles:
    - { role: nginx, when: enable_nginx_lb and load_balancer_is_required }
    - { role: apache, when: enable_apache_lb and load_balancer_is_required }`

![alt text](./Images/pic%206.jpg)

- Add this to the site.yml file
 `
 - name: Loadbalancers assignment
       hosts: lb
         - import_playbook: ../static-assignments/loadbalancers.yml
        when: load_balancer_is_required`

![alt text](./Images/pic%204.jpg)

- Now we can make use of env-vars\uat.yml file to define which loadbalancer to use in UAT the environment by setting the respective environmental variable to true.

We will activate load balancer, and enable nginx by setting these in the respective environment’s env-vars file.

 `enable_nginx_lb: true
load_balancer_is_required: true`

![alt text](./Images/pic%207.jpg)

- The same must work with apache LB, so we can switch it by setting respective environmental variable to true and other to false.

To test this, we can update inventory for each environment and run Ansible against each environment using:

 `ansible-playbook -i inventory/uat.yml playbooks/site.yml`