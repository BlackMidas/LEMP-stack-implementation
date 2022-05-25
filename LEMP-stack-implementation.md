# Project 2
## LEMP Web Stack Implementation

### **Installing the Nginx Web Server**

I started off by updating the server’s package index using the below command

`sudo apt update`
![alt text](./images/1-sudo-apt-update.PNG)

Next, I use apt install to get Nginx installed:

`sudo apt install nginx`
![alt text](./images/2-sudo-apt-install-nginx.PNG)

to verify that nginx was successfully installed and is running as a service in Ubuntu, the command below was used. If it shows green and running, it is OK.

`sudo systemctl status nginx`
![alt text](./images/3-sudo-systemctl-status-nginx.PNG)

Our server is running and to access it locally in our Ubuntu shell, run:

`curl http://localhost:80`
![alt text](./images/4-curl-http-127.0.0.1-80.PNG)

Now  to test how our Nginx server can respond to requests from the Internet. Open a web browser and try to access using this url http://Public-IP-Address:80. The output is shown below.
![alt text](./images/5-ipaddress-80.PNG)

### **Installing mysql**
Using ‘apt’ command to acquire and install mysql software by running:
`sudo apt install mysql-server`
![alt text](./images/6-sudo-apt-install-mysql-server.PNG)

 Next, log in to the MySQL console by typing:
`sudo mysql`
![alt text](./images/7-sudo-mysql.PNG)

To set a password for the root user, using mysql_native_password as default authentication method and defining the user’s password defind here as PassWord.1

`ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';`
![alt text](./images/8-set-root-user-password.PNG)

Exit the mysql shell by entering `exit` which return to the ubuntu terminal
To start running the recommended interavtive security script that comes pre-installed with MySQL, the below command is ran

`sudo mysql_secure_installation`
![alt text](./images/9-sudo-mysql-secure-installation.PNG)

Enter yes to setup validate password plugin. If you answer “yes”, you’ll be asked to select a level of password validation. click through 'yes' for the the rest questions.

When you’re finished, test if you’re able to log in to the MySQL console by typing:

`sudo mysql -p`

### **Installing PHP**
To install php with the 2 necessary external packages at once, the code below was used.
`sudo apt install php-fpm php-mysql`
![alt text](./images/10-sudo-apt-install-php-mysql-php-fpm.PNG)

Creating the root web directory for the domain as follows:

### **Configuring Nginx to use PHP processor**
`sudo mkdir /var/www/projectLEMP`

Next, ownership of the directory was assigned with the $USER environment variable, which will reference the current system user:

`sudo chown -R $USER:$USER /var/www/projectLEMP`

Then, opening a new configuration file in Nginx’s sites-available directory using nano editor:

`sudo nano /etc/nginx/sites-available/projectLEMP`

This creates a new blank file where the following bare-bones configuration was entered as in image below:
![alt text](./images/11-sudo-nano-bare-bones-configurations.PNG)

Next, activating the configuration by linking to the config file from Nginx’s sites-enabled directory:

`sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/`

You can test your configuration for syntax errors by typing:
`sudo nginx -t` and verifying with the output:
![alt text](./images/16-sudo-nginx-t.PNG)

We also need to disable default Nginx host that is currently configured to listen on port 80, for this run:

`sudo unlink /etc/nginx/sites-enabled/default`

Finally, Nginx reloaded to apply the changes using:

`sudo systemctl reload nginx`

 The website is now active though the web root /var/www/projectLEMP is still empty. Creating an index.html file in that location so that the new server block can be tested to work as expected. Run below code:

`sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html`

Now go to your browser and try to open the website URL using http://Public-DNS-Name:80 or IP-address. Th output is shown in the image below.
![alt text](./images/19-ipaddress-80.PNG)


### **Testing PHP with Nginx**
Testing it to validate that Nginx can correctly hand .php files off to the PHP processor by creating a test PHP file in the document root. A new file called info.php within the document root is opned in nano editor:

`sudo nano /var/www/projectLEMP/info.php`

Then the following php code entered to the editor
`<?php phpinfo();`

This page can now be accessed in a browser by visiting the domain name or public IP address set up in the Nginx configuration file, followed by /info.php which gives th output below
![alt text](./images/21-ipaddress-80-php.PNG/)

After checking the relevant information about the PHP server the file created was deletd using 'rm' command below as it contains sensitive information about the PHP environment and Ubuntu server.

`sudo rm /var/www/your_domain/info.php`

### **Retrieving data from MySQL database with PHP**

First, connecting to the MySQL console using the root account:

`sudo mysql -p`   

To create a new database, run the following command from your MySQL console:

`CREATE DATABASE ``example_database``;`

The following command creates a new user named example_user, using mysql_native_password as default authentication method. We’re defining this user’s password as password, but you should replace this value with a secure password of your own choosing.

`CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';`

Now we need to give this user permission over the example_database database:

`GRANT ALL ON example_database.* TO 'example_user'@'%';`

Now exit the MySQL shell with:

`exit`

You can test if the new user has the proper permissions by logging in to the MySQL console again, this time using the custom user credentials:

`mysql -u example_user -p`

 After logging in to the MySQL console, confirm that you have access to the example_database database:

`SHOW DATABASES;`
This will give you the following output:
![alt text](./images/27-user-confirm-access-db.PNG)

Next, we’ll create a test table named todo_list. From the MySQL console, run the following statement:

`CREATE TABLE example_database.todo_list (item_id INT AUTO_INCREMENT, content VARCHAR(255), PRIMARY KEY(item_id));`

Insert a few rows of content in the test table using command below. Repeating the command a few times, using different VALUES:

`INSERT INTO example_database.todo_list (content) VALUES ("My first important item");`
As done in the image below:
![alt text](./images/29-insert-into-table.PNG)

To confirm that the data was successfully saved to the table, run:

`SELECT * FROM example_database.todo_list;`
You will see the following output:
![alt text](./images/31.PNG)

After confirming that you have valid data in your test table, you can exit the MySQL console:
`exit`

Now you can create a PHP script that will connect to MySQL and query for the content. create a new PHP file in your custom web root directory using your preferred editor. I’ll use vi for that:

`sudo vi /var/www/projectLEMP/todo_list.php`

Copy the codes in the image into your todo_list.php script:
![alt text](./images/30-nano-php-todo-list.PNG)

Again, this page can now be accessed in a web browser by visiting the domain name or public IP address set up in the Nginx configuration file, followed by /todo_list.php: the output shown below.

![alt text](./images/31-php-mysql.PNG)