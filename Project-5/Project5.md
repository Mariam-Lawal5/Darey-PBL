### PROJECT 5: IMPLEMENTATION OF A CLIENT SERVER ARCHITECTURE USING MYSQL DATABASE MANAGEMENT SYSTEM (DBMS)

The project is to implement a Client-Server Architecture using MySQL Database Management System (DBMS)

Client-Server refers to an architecture in which two or more computers are connected together over a computer network to send and receive requests between one another.

In this communication, each machine has its own role: the machine sending requests is usually referred as "Client" and the machine responding to the request is called "Server".

![client-server model](./Images/client-server%20Architecture.png)

> Create and configure two Linux-based virtual servers (EC2 instances in AWS).

![EC2 Instances](./Images/virtual%20network.png)


**Step 1 – Database Server**

> On the database server update the server and install MySQL Server

 `sudo apt update -y`

 `sudo apt install mysql-server`

 > Enable the service

 `sudo systemctl enable mysql`

 ![mysql enabled](./Images/mysql%20enabled.pngimage.jpg)

> Update the inbound rules in MYSQL server, by adding TCP port 3306. For extra security allow access only to private IP address of MYSQL client.

![security group](./Images/Security%20group%20update.png)

> Run the security script in my MySQL server

 `sudo mysql_secure_installation`

> Run MySQL using :

  `sudo mysql`

![sCREENSHOT](./Images/Run%20MySQL.png)

> Configure MySQL server to allow connections from remote hosts, replace ‘127.0.0.1’ to ‘0.0.0.0’

 `sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf`

> Restart mysql

 `sudo systemctl restart mysql`


**Step 2 – Client-Server**

> On the Client server update the server and install MySQL Server

 `sudo apt update -y`

 `sudo apt install mysql-client`

> On  the client Linux Server connect remotely to mysql server Database Engine:

 `sudo mysql -u remote_user -h <ip address> -p`

> Confirm successfull connection to the remote MySQL server and perform SQL queries

 `Show databases;`

 ![alt text](./Images/Client%20server.png)
