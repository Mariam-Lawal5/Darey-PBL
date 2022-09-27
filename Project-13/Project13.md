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

On Jenkins-Ansible server make sure that git is installed with git --version, then go to ‘ansible-config-mgt’ directory and run.

git init
git pull https://github.com/<your-name>/ansible-config-mgt.git
git remote add origin https://github.com/<your-name>/ansible-config-mgt.git
git branch roles-feature
git switch roles-feature
Inside roles directory create your new MySQL role with ansible-galaxy install geerlingguy.mysql and rename the folder to mysql

mv geerlingguy.mysql/ mysql
Read README.md file, and edit roles configuration to use correct credentials for MySQL required for the tooling website.

Roles -> MYSQL -> Defaults -> Main.yml