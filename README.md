# WEB STACK IMPLEMENTATION (LEMP STACK) IN AWS

### STEP 1 Provisioning a new EC2 instance of t2.micro family with Ubuntu Server 24.04 LTS (HVM) For Web-Server:

![image](https://github.com/user-attachments/assets/67063861-a70d-4d2d-8b07-98211bccdda1)

#### Accessing the Machine Using Private Key:

```
ssh -i .\New1.pem ubuntu@3.111.214.134
```

### STEP 2 Installing the Nginx Web Server

#### Updating the Machine and Installing the Nginx Web-server:

```
sudo apt update -y
```
![image](https://github.com/user-attachments/assets/6928f2eb-1571-4b1e-a037-40a597625ca0)


```
sudo apt install nginx -y
```
![image](https://github.com/user-attachments/assets/c0aca849-63a7-4fd8-a81b-91db678756aa)

#### Checking Status of Nginx Web-server Running or Using Following Commands:
```
sudo systemctl status nginx
```

![image](https://github.com/user-attachments/assets/2624db51-799b-444d-ba02-5da5cba5fcad)

#### Accessing the Nginx web-server on Web-browser:
```
http://3.111.214.134/
```
![image](https://github.com/user-attachments/assets/678a5cea-5c3f-4f49-806f-4892f487be05)

### Step 3 — Installing MySQL

#### To Store Relational Database Installing the mysql-server Using Following Command:
```
sudo apt install mysql-server -y 
```
![image](https://github.com/user-attachments/assets/ce6bfa98-3444-44f1-b19c-a6aab3b6fdc7)

#### Log in to the MySQL console by typing and Creating the Password for Authentication:

```
sudo mysql
```
![image](https://github.com/user-attachments/assets/46bcbb0a-dcb9-49bb-a17d-31c13b441bef)

```
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'Golu123!@#';
```
![image](https://github.com/user-attachments/assets/99cd9066-bf84-4a26-8f8b-20f50957f106)

##### Exit the MySQL shell with:
```
exit
```
#### Login to mysql Database Sever using following command to Check the Authentication with new Password:
```
sudo mysql -p
```

### Step 4 – Installing PHP to process code and generate dynamic content for the web server.

Note:- While Apache have the PHP interpreter in each request, Nginx requires an external program to handle PHP processing and act as a bridge between the PHP interpreter itself and the web server. This allows for a better overall performance in most PHP-based websites, but it requires additional configuration. we’ll need to install php-fpm, which stands for “PHP fastCGI process manager, and tell Nginx to pass PHP requests to this software for processing. Additionally, we’ll need php-mysql, a PHP module that allows PHP to communicate with MySQL-based databases. Core PHP packages will automatically be installed as dependencies.

#### To install these 2 packages at once, run:

```
sudo apt install php-fpm php-mysql
```
![image](https://github.com/user-attachments/assets/dcc1c6b5-c3a7-4112-8d61-199d774a5a82)

#### Checking the Version of PHP using following command:

```
php -v
```
![image](https://github.com/user-attachments/assets/7a2381ed-bf2a-4ec0-861d-0d5bc281fe7a)

### Step 4 — Configuring Nginx to Use PHP Processor

- When using the Nginx web server, we can create server blocks (similar to virtual hosts in Apache) to encapsulate configuration details and host more than one domain on a single server. In this guide, we will use projectLEMP as an example domain name.

- On Ubuntu 24.04, Nginx has one server block enabled by default and is configured to serve documents out of a directory at /var/www/html. While this works well for a single site, it can become difficult to manage if you are hosting multiple sites. Instead of modifying /var/www/html, we’ll create a directory structure within /var/www for the your_domain website, leaving /var/www/html in place as the default directory to be served if a client request does not match any other sites.

### a) Create the root web directory for domain as follows:

```
sudo mkdir /var/www/projectLEMP
```

### b) Chaning the ownership so that default user able to execute projectLEMP directory:
```
sudo chown -R $USER:$USER /var/www/projectLEMP
```
### c) Opening a new configuration file in Nginx’s sites-available and configuring using following Configuration:

```
sudo nano /etc/nginx/sites-available/projectLEMP
```

```
server {

        listen 80;
        server_name projectLEMP www.projectLEMP;
        root /var/www/projectLEMP;

        index index.html index.htm index.php;

        location / {
                try_files $uri $uri/ =404;

        }

        location ~ \.php$ {
                include snippets/fastcgi-php.conf;
                fastcgi_pass unix:/var/run/php/php8.3-fpm.sock;

        }

        location ~ /\.ht {
                deny all;
        }

}

```

## Key elements explained:
- listen 80;: The server listens on port 80 (HTTP).
- server_name projectLEMP www.projectLEMP;: Defines the domain names this server will respond to.
- root /var/www/projectLEMP;: The root directory where your website files are located.
- index index.html index.htm index.php;: Specifies the index files to be served.
- location ~ \.php$ — This location block handles the actual PHP processing by pointing Nginx to the fastcgi-php.conf configuration file and the php7.4-fpm.sock file, which declares what socket is associated with php-fpm.
- try_files $uri $uri/ =404;: Attempts to serve the requested URI. If not found, returns a 404 error.
- fastcgi_pass unix:/var/run/php/php8.3-fpm.sock;: Passes PHP requests to PHP-FPM, using a Unix socket for PHP 8.3.

![image](https://github.com/user-attachments/assets/0d46b5cb-3027-4964-95de-7e91d2e63a18)

### d) Checking the syntax of Configuartion file.
```
sudo nginx -t
```
### e) Disable default Nginx host that is currently configured.
```
sudo unlink /etc/nginx/sites-enabled/default
```

### f) Reloading nginx so that it will reload the configuration which we configure so far.
```
sudo systemctl reload nginx
```
![image](https://github.com/user-attachments/assets/fed77d87-7c54-4a55-a476-6e3c39dc7efe)

### Step 6 – Testing PHP with Nginx

- At this point, LEMP stack is completely installed and fully operational. We can test it to validate that Nginx can correctly handle .php files off to  PHP processor. We can do this by creating a test PHP file in document root. Open a new file called info.php within your document root

```
nano /var/www/projectLEMP/info.php
```

- Writing PHP script inside the info.php
  
```
<?php
phpinfo();
?>
```
- Accessing the PHP Website
![image](https://github.com/user-attachments/assets/f9d032eb-0181-488f-9ef9-d94005d2511a)

### Step 7 — Retrieving data from MySQL database with PHP
- we will create a test database (DB) with simple "To do list" and configure access to it, so the Nginx website would be able to query data from the DB and display it.
- At the time of this writing, the native MySQL PHP library mysqlnd doesn’t support caching_sha2_authentication, the default authentication method for MySQL 8. We’ll need to create a new user with the mysql_native_password authentication method in order to be able to connect to the MySQL database from PHP.
- We will create a database named tester and a user named tester,
