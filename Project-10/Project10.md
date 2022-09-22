# LOAD BALANCER SOLUTION WITH NGINX AND SSL/TLS

During this project we will do the following:

- Register a new domain name and connect to route 53

- Configure Nginx as a Load Balancer

![alt text](./Images/pic%201.png)

# Step 1 -Configure Nginx As A Load Balancer

In order to do this complete the following:

- Create an EC2 VM based on Ubuntu Server 20.04 LTS and open TCP port 80 for HTTP connections and TCP port 443

- Update /etc/hosts file for local DNS with Web Servers’ names (e.g. Web1 and Web2) and their local IP addresses

 `<IP web 1>`

 `<IP web 2>`

- Update the server repositry and install nginx:

 `sudo apt update && sudo apt install nginx`

In order to Configure Nginx LB using Web Servers’ names defined in /etc/hosts created a config file by executing the below command:

 `sudo vi /etc/nginx/nginx.conf`

 Or below screenshot

 ![alt text](./Images/pic%202.png)

- Insert and save the configuration below the http section:

 ` upstream myproject {
    server Web1 weight=5;
    server Web2 weight=5;
  }

server {
    listen 80;
    server_name www.mariamtooling.site;
    location / {
      proxy_pass http://myproject;
    }
  }`

![alt text](./Images/pic%206.png)

![alt text](./Images/pic%203.png)

- Check that nginx was successfully configured:

 `sudo nginx -t`

![alt text](./Images/pic%204.png)
![alt text](./Images/pic%205.png)

- Restart Nginx and make sure the service is up

 `sudo systemctl restart nginx`

 `sudo systemctl status nginx`

# Step 2- Register A New Domain Name & Configure Secured Connection Using Ssl/Tls Certificates.

- Register a doman with any registrar of your choice

- Go unto the route 53 dashboard on AWS and create a hosted zone, connect this to your domain. This will tell route 53 to respond to DNS queries from your domain.

- Assign an Elastic IP to your Nginx LB server and associate the domain name with this Elastic IP, the purpose of this is to ensure that the public IP address is static because everytime you restart, stop or start your EC2 instance, you get a new public IP address. When you want to associate your domain name, it is better to have a static IP address that does not change after reboot, thus Elastic IP is the solution for this problem.

![alt text](./Images/pic%207.png)

- Configure secured connection using SSL/certificates

- Install certbot and dependencies by executing the following command:

 `sudo apt install certbot -y`

 `sudo apt install python3-certbot-nginx -y`

- Execute the below commands to checked syntax and reload nginx:

 `sudo nginx -t && sudo nginx -s reload`

- Make sure snap service is running 

 `sudo systemctl status snapd`

![alt text](./Images/pic%208.png)

- Create a certificate for your domain to make it is secure:

 `sudo certbot --nginx -d www.mariamtooling.site`

 ![alt text](./Images/pic%209.png)

- Enter a valid agreement and accept service agreement. To increase security select for incoming request from port 80 to be redirected to port 443.
screenshot below showing site is secure

![alt text](./Images/pic%2010.png)

- Create a cron assignment so that the certificate will automatically renew each time it expires by executing this command:

 `crontab -e`

- Insert the below command which will be executed every 12 minutes of every hour:

 `* */12 * * *   root /usr/bin/certbot renew > /dev/null 2>&1`

- Set up periodical renewal of your SSL/TLS certificate. By default, LetsEncrypt certificate is valid for 90 days, so it is recommended to renew it at least every 60 days or more frequently.

- You can test renewal command in dry-run mode

 `sudo certbot renew --dry-run`

- The best practice is to have a scheduled job to run renew command periodically. Add the following command

 `* */12 * * * root /usr/bin/certbot renew > /dev/null 2>&1`