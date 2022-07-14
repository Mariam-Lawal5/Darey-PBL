# WEB STACK IMPLEMENTATION (LAMP STACK) 

A LAMP Stack is a set of open-source software that can be used to create websites and web applications. LAMP is an acronym, and these stacks typically consist of the Linux operating system, the Apache HTTP Server , MySQL and the PHP Programming language.

## Step 1- Install Apache 

- Update Respositories and Install Apache2 using Ubuntu

 `sudo apt update`

 `sudo apt install apache2`

- Verify that apache2 is running as a Service in the OS using code:

 `sudo systemctl status apache2`

![Apache2 Status](./Images/Apache%20running.png)

- Confirm Apache HTTP access on port 80:

 `curl http://localhost:80`

- Verify Apache HTTP Server can respond to requests from the internet using :

 `curl -s http://169.254.169.254/latest/meta-data/public-ipv4`
 
![Apache result](./Images/Apache%20result.png)


## Step 2- Install MYSQL

MySQL is a popular relational database management system used within PHP environments.

- Install MySQL using:

 `sudo apt install mysql-server`
 
- Log in to the MySQL console using:

 `sudo mysql`

- Then exit the MySQL with:

`mysql> exit`


- Run a security script that comes Pre-installed with MySQL:

 `sudo mysql_secure_installation`

- Test connection by logging into  MySQL using:

 `sudo mysql -p`


- Then exit the MySQL with:

`mysql> exit`

![SQL Screenshot](./Images/SQL%20Screenshot.png)


## Step 3- Install PHP

PHP is the component of our setup that will process code to display dynamic content to the end user.

- In addition to the php package, php-mysql is needed, a PHP module that allows PHP to communicate with MySQL-based databases. 

- libapache2-mod-php is required to enable Apache to handle PHP files. Core PHP packages will automatically be installed as dependencies.

- To install these 3 packages at once, run:

 `sudo apt install php libapache2-mod-php php-mysql`

- Confirm the PHP version is running:

 `php -v`

![PHP Status](./Images/PHP%20Status.png)


## Step 4- CREATING A VIRTUAL HOST FOR YOUR WEBSITE USING APACHE

The objective is to set up a domain called **projectlamp**

Apache on Ubuntu 20.04 has one server block enabled by default that is configured to serve documents from the /var/www/html directory.

- Create the directory for projectlamp using ‘mkdir’ command as follows:

 `sudo mkdir /var/www/projectlamp`

- Assign ownership to the directory with your current system user:

 `sudo chown -R $USER:$USER /var/www/projectlamp`

- Create a new configuration file in Apache’s sites-available directory:

 `sudo vi /etc/apache2/sites-available/projectlamp.conf`

![Configuration file](./Images/Configuration%20file.png)

- Insert the following bare-bones configuration :

 `<VirtualHost *:80>
    ServerName projectlamp
    ServerAlias www.projectlamp 
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/projectlamp
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>`

- Save and close the file, following the steps below:

1. Hit the esc button on the keyboard
2. Type :
3. Type wq. w for write and q for quit
4. Hit ENTER to save the file

- Use the ls command to show the new file in the sites-available directory

`sudo ls /etc/apache2/sites-available`

- With this VirtualHost Configuration, we're telling Apache to serve projectlamp as its web root directory.

	![Result status](./Images/Result%20status.png)

- Enable the new virtual host:

 `sudo a2ensite projectlamp`

- Disable the default website that comes installed with Apache. This is required if you’re not using a custom domain name, because Apache’s default configuration would overwrite your virtual host:

 `sudo a2dissite 000-default`

- To ensure configuration file doesn’t contain syntax errors, run:

 `sudo apache2ctl configtest`

- Reload Apache to effect changes:

 `sudo systemctl reload apache2`

- Create an index.html file in that location so that we can test that the virtual host works as expected:

`sudo echo 'Hello LAMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectlamp/index.html`

Go to browser and open the website URL using IP address:

http://<Public-IP-Address>:80

![result](./Images/result.png)


## Step 5- ENABLE PHP ON THE WEBSITE

With the default DirectoryIndex settings on Apache, a file named index.html will always take precedence over an index.php file.To change this behavior, you’ll need to edit the /etc/apache2/mods-enabled/dir.conf file and change the order in which the index.php file is listed within the DirectoryIndex directive:

 `sudo vim /etc/apache2/mods-enabled/dir.conf`

 `<IfModule mod_dir.c>
        #Change this:
        #DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
        #To this:
        DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>`


- Reload Apache so the changes take effect:

 `sudo systemctl reload apache2`

![PHP website](./Images/PHP%20website.png)