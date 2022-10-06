# EXPERIENCE CONTINUOUS INTEGRATION WITH JENKINS | ANSIBLE | ARTIFACTORY | SONARQUBE | PHP









k
 ```
 pipeline {
  agent any

  stages {
      stage("Initial cleanup") {
        steps {
         dir("${WORKSPACE}") {
              deleteDir()
            }
          }
        }
     
    stage('Build') {
      steps {
        script {
          sh 'echo "Building Stage"'
        }
      }
    }

    stage('Test') {
      steps {
        script {
          sh 'echo "Testing Stage"'
        }
      }
    }
    
    stage('Package') {
      steps {
        script {
          sh 'echo "Packaging App"'
        }
      }
    }

    stage('Deploy') {
      steps {
        script {
          sh 'echo "Deploying to Dev"'
        }
      }
    }

    stage("Clean Up") {
      steps {
        cleanWs()
            }
          }
        }
}
 ```


# RUNNING ANSIBLE PLAYBOOK FROM JENKINS


- After install ansible run the following dependencies, to enable ansible to work properly, since we have some ansible modules in the roles.
Run the below as root user using `sudo su -`

 `apt install python3 python3-pip wget unzip git -y

 python3 -m pip install --upgrade setuptools

 python3 -m pip install --upgrade pip

 python3 -m pip install PyMySQL

 python3 -m pip install mysql-connector-python

 python3 -m pip install psycopg2==2.7.5 --ignore-installed
 
 ansible-galaxy collection install community.postgresql`

 Then Install ansible plugin in Jenkins

 - Clear the Jenkfile and ----

- In the deploy folder create a file called **ansible.cfg** and paste the following in the file

 ```
 [defaults]
timeout = 160
callback_whitelist = profile_tasks
log_path=~/ansible.log
host_key_checking = False
gathering = smart
ansible_python_interpreter=/usr/bin/python3
allow_world_readable_tmpfiles=true

[ssh_connection]
ssh_args = -o ControlMaster=auto -o ControlPersist=30m -o ControlPath=/tmp/ansible-ssh-%h-%p-%r -o ServerAliveInterval=60 -o ServerAliveCountMax=60 -o ForwardAgent=yes
 ```

By default when we install ansible, we have the default configuration file in /etc/ansible/ansible.cfg, now we are creating our own config file to define the default thing of our choice, how we want the ansible to run, variables to use etc.

Jenkins works with file inside the workspace folder.

By default when we install ansible, we have the default configuration file in /etc/ansible/ansible.cfg, now we are creating our own config file to define the default thing of our choice, how we want the ansible to run, variables to use etc.

Jenkins works with file inside the workspace folder.

The role path is not specified in the ansible.cfg file we created, it will be defined in the Jenkinsfile.

Jenkins needs to export the ANSIBLE_CONFIG environment variable, which must be declared globally and specifies where ansible.cfg file is.

 ```
environment {
  ANSIBLE_CONFIG="${WORKSPACE}/deploy/ansible.cfg"
 }
 ```

- To run Ansible playbook via the Jenkinsfile, go to Dashoard -> Manage Jenkins -> Manage Credentials -> Credentials -> Add Credentials. Fill in the blanks, by entering content of the pem key and username (ubuntu or ec-user) To ensure our ansible run against inventory/dev

![alt text](./Images/Pic%207.jpg)


- Add the path to ansible executable directory /usr/bin:

![alt text](./Images/Pic%208.jpg)

- Go to Dashboard -> ansib-config -> Pipeline Syntax, configure path to the playbook and inventory path, ssh-user and colorized output. Generate pipeline script, copy the script and paste in the Jenkinsfile.

![alt text](./Images/Pic%209.jpg)
![alt text](./Images/Pic%2010.jpg)
![alt text](./Images/Pic%2011.jpg)
![alt text](./Images/Pic%2012.jpg)

- Copy the content below into the Jenkinsfile.

 ```
 pipeline {
  agent any

  environment {
      ANSIBLE_CONFIG="${WORKSPACE}/deploy/ansible.cfg"
    }

  parameters {
      string(name: 'inventory', defaultValue: 'dev',  description: 'This is the inventory file for the environment to deploy configuration')
    }

  stages{
      stage("Initial cleanup") {
          steps {
            dir("${WORKSPACE}") {
              deleteDir()
            }
          }
        }

      stage('Checkout SCM') {
         steps{
            git branch: 'feature/jenkinspipeline-stages', url: 'https://github.com/Mariam-Lawal5/ansible-configP14.git'
         }
       }

      stage('Prepare Ansible For Execution') {
        steps {
          sh 'echo ${WORKSPACE}' 
          sh 'sed -i "3 a roles_path=${WORKSPACE}/roles" ${WORKSPACE}/deploy/ansible.cfg'  
        }
     }

      stage('Run Ansible playbook') {
        steps {
           ansiblePlaybook become: true, colorized: true, credentialsId: 'private-key', disableHostKeyChecking: true, installation: 'ansible', inventory: 'inventory/dev', playbook: 'playbooks/site.yml'
         }
      }

      stage('Clean Workspace after build'){
        steps{
          cleanWs(cleanWhenAborted: true, cleanWhenFailure: true, cleanWhenNotBuilt: true, cleanWhenUnstable: true, deleteDirs: true)
        }
      }
   }

}
 ```

- Update the inventory/dev with 2 instances (nginx and db) and playbook.
Push the code with the new updates in the Jenkinsfile.

- I had an error running ansible playbook stage. the error was due to the screen shot.

![alt text](./Images/Pic%2014.jpg)

- Resolved the error by replacing the playbook Line 3 from **ansible.builtin.import_playbook** with just **import_playbook** as see below:

![alt text](./Images/Pic%2015.jpg)

![alt text](./Images/Pic%2016.jpg)