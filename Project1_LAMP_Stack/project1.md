# Step 0 - Preparing prerequisites
# 1. Launch a new EC2 instance of t2.micro Family with Ubuntu Server 20.04 LTS

* [x] Select your preferred Region (the closest to you)
* [x] Launch a new EC2 instance of t2.micro
* [x]Family with Ubuntu Server 20.04 LTS
  * Security Groups -> SSH, TCP protocol: 22, Source: Enywhere
  * Save pem file securely and do not share it with anyone

# 2. Connecting to EC2 terminal

* [x] Use the pem key file to connect to the EC2 Instance via ssh
* [x] Connect to the instance by running 
        ssh -i <private-key-name>.pem ubuntu@<Public-IP-address>
    * Note: You can use Windows terminal, Gitbash, MobaXterm, Putty, applications to connect to the EC2 Instance in AWS
    * Note: Please read information about AWS free tier limits and make sure that you STOP your EC2 instance when you are not using it.
        *  All we need to know right now is that we can use 750 hours (31.25 days) of t2.micro server per month for the first 12 months FOR FREE
        * You can launch and stop new instances when you need to, but by default there is a soft-limit of maximum 5 running instances at the same time. In our first projects we will be using only 1 running instance at a time. When you stop an instance – it stops consuming available hours.
        * Note that every time you stop and start your EC2 instance – you will have a new IP address, it is normal behavior, so do not forget to update your SSH credentials when you try to connect to your EC2 server.

# Step 1. Installing Apache and updating the firewall
### What is Apache? Apache HTTP Server is an open source software, the most widely used web server software.

## 1. Install Apache using Ubuntu package manager
* [x] Update a list of packages in package manager
    * sudo apt update
* [x] Run apache2 package installation
    * sudo apt install apache2
* Note: If it is green and running, then you did everything correctly – you have just launched your first Web Server in the Clouds!
* [x] Open TCP port 80, which is the default port that web browser use to access pages on the Internet
* [x]Add a rule to EC2 instance configuration to open inbound connection through port 80
*image.png
* test how our Apache HTTP server can respond to requests from the Internet
* [x] Open a web browser of your choice and try yo access following url
    * http://<Public-IP-Address>:80
    * Note: If you see a page displaying the "Apache2 Ubuntu Default Page ... It works" then the web server is now correctly installed and accesible though your firewall. 
    In fact, it is the same content that you previously got by ‘curl’ command, but represented in nice HTML formatting by your web browser.

## Step 2 Installing MySqL
### Now that you have a web server up and running, you need to install a Database Management System (DBMS) to be able to store and manage data for your site in a relational database. MySQL is a popular relational database management system used within PHP environments, so we will use it in our project.

* [x] Use 'apt' to acquire and install this software
    * sudo apt install mysql-server
        * Note: When prompted, confirm installation by typing Y, and then ENTER.
* [x] run security script
    * ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';
        * Note: This script will remove some insecure default settings and lock down access to your database system. Before running the script you will set a password for the root user, using mysql_native_password as default authentication method. We’re defining this user’s password as PassWord.1.
* [x] Exit the MySql shell with 
    * mysql > exit
* [x] Start the interactive script by running
    * sudo mysql_secure_installation
    * Answer Y for yes, or anything else to continue without enabling
* [x] Levels of password
    * There are three levels of password validation policy:

        LOW    Length >= 8
        MEDIUM Length >= 8, numeric, mixed case, and special characters
        STRONG Length >= 8, numeric, mixed case, special characters and dictionary              file

        Please enter 0 = LOW, 1 = MEDIUM and 2 = STRONG: 1
    * If you enabled password validation, you’ll be shown the password strength for the root password you just entered and your server will ask if you want to continue with that password. If you are happy with your current password, enter Y for “yes” at the prompt:

    Estimated strength of the password: 100 
    Do you wish to continue with the password provided?(Press y|Y for Yes, any other key for No) : y
* [x] Testing your log in to the MyAql console
    * sudo mysql -p
* [x] To exit the MySql console:
    * mysql> exit
    * Note: It is required to provide a password to connect as the root user.
    * Note: At the time of this writing, the native MySQL PHP library mysqlnd doesn’t support caching_sha2_authentication, the default authentication method for MySQL 8. For that reason, when creating database users for PHP applications on MySQL 8, you’ll need to make sure they’re configured to use mysql_native_password instead.

### Your MySQL server is now installed and secured. Next, we will install PHP, the final component in the LAMP stack.

## Step 3 Installing PHP
### You have Apache installed to serve your content and MySQL installed to store and manage your data. PHP is the component of our setup that will process code to display dynamic content to the end user. In addition to the php package, you’ll need php-mysql, a PHP module that allows PHP to communicate with MySQL-based databases. You’ll also need libapache2-mod-php to enable Apache to handle PHP files. Core PHP packages will automatically be installed as dependencies.

* [x] To install these 3 packages at once :
    * sudo apt install php libapache2-mod-php php-mysql
* [x] to confirm your PHP version:
    * php -v
    * image.png
    * At this point, your LAMP stack is completely installed and fully operational
    * image.png

## Step 4 Creating a Virtual Host for your Website using Apache
### In this project, you will set up a domain called projectlamp, but you can replace this with any domain of your choice.

### Apache on Ubuntu 20.04 has one server block enabled by default that is configured to serve documents from the /var/www/html directory.
### We will leave this configuration as is and will add our own directory next next to the default one.

* [x] Create the directory for project using 'mkdir' command:
    * sudo mkdir /var/www/projectlamp
* [x] Assign ownership of the directory with your current system user:
    * sudo chown -R $USER:$USER /var/www/projectlamp
* [x] create and open a new configuration file in Apache’s sites-available directory using your preferred command-line editor
    * sudo vi /etc/apache2/sites-available/projectlamp.conf
    *  paste the text: 
        * <VirtualHost *:80>
                ServerName projectlamp
                ServerAlias www.projectlamp 
                ServerAdmin webmaster@localhost
                DocumentRoot /var/www/projectlamp
                ErrorLog ${APACHE_LOG_DIR}/error.log
                CustomLog ${APACHE_LOG_DIR}/access.log combined
          </VirtualHost>
    * Save and close the file, esc + :wq!, hit ENTER
* [x] use the ls command to show the new file in the sites-available directory
    * sudo ls /etc/apache2/sites-available
    * You will see something like this: 
        * 000-default.conf  default-ssl.conf  projectlamp.conf
    * Note: With this VirtualHost configuration, we’re telling Apache to serve projectlamp using /var/www/projectlampl as its web root directory. If you would like to test Apache without a domain name, you can remove or comment out the options ServerName and ServerAlias by adding a # character in the beginning of each option’s lines. Adding the # character there will tell the program to skip processing the instructions on those lines.
* [x] use a2ensite command to enable the new virtual host
    * sudo a2ensite projectlamp
* [x] You might want to disable the default website that comes installed with Apache. To disable Apache’s default website use a2dissite command
    * sudo a2dissite 000-default
* [x] To make sure your configuration file doesn’t contain syntax errors
    * sudo apache2ctl configtest
* [x] reload Apache so these changes take effect
    * sudo systemctl reload apache2
* [x] Your new website is now active, but the web root /var/www/projectlamp is still empty. Create an index.html file in that location so that we can test that the virtual host works as expected:
    * sudo echo 'Hello LAMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectlamp/index.html
* [x] Open your website URL using IP address
    * http://<Public-IP-Address>:80
    * If you see the text from ‘echo’ command you wrote to index.html file, then it means your Apache virtual host is working as expected.
In the output you will see your server’s public hostname (DNS name) and public IP address. You can also access your website in your browser by public DNS name, not only by IP – try it out, the result must be the same (port is optional)

## Step 5 Enable PHP on the Website

### With the default DirectoryIndex settings on Apache, a file named index.html will always take precedence over an index.php file. This is useful for setting up maintenance pages in PHP applications, by creating a temporary index.html file containing an informative message to visitors. Because this page will take precedence over the index.php page, it will then become the landing page for the application. Once maintenance is over, the index.html is renamed or removed from the document root, bringing back the regular application page.

* [x] In case you want to change this behavior, you’ll need to edit the /etc/apache2/mods-enabled/dir.conf file and change the order in which the index.php file is listed within the DirectoryIndex directive:
    * sudo vim /etc/apache2/mods-enabled/dir.conf
    * <IfModule mod_dir.c>
        #Change this:
        #DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
        #To this:
        DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
      </IfModule>
* [x] you will need to reload Apache so the changes take effect
    * sudo systemctl reload apache2
* [x] we will create a PHP script to test that PHP is correctly installed and configured on your server.
* [x] Now that you have a custom location to host your website’s files and folders, we’ll create a PHP test script to confirm that Apache is able to handle and process requests for PHP files. Create a new file named index.php inside your custom web root folder
 * vim /var/www/projectlamp/index.php
* [x] This will open a blank file. Add the following text, which is valid PHP code, inside the file
    * <?php
      phpinfo();
* When you are finished, save and close the file, refresh the page and you will see a page similar to this:
    * image.png
* [x] This page provides information about your server from the perspective of PHP. It is useful for debugging and to ensure that your settings are being applied correctly.
    * If you can see this page in your browser, then your PHP installation is working as expected.
* [x] After checking the relevant information about your PHP server through that page, it’s best to remove the file you created as it contains sensitive information about your PHP environment -and your Ubuntu server. You can use rm to do so:
    * sudo rm /var/www/projectlamp/index.php

    ![This guide was inspired by Digital Ocean] (https://www.digitalocean.com/community/tutorials/how-to-install-linux-apache-mysql-php-lamp-stack-on-ubuntu-20-04#step-3-%E2%80%94-installing-php)

# Congratulations! You have finished your very first REAL LIFE PROJECT by deploying a LAMP stack website in AWS Cloud!
* image.png



