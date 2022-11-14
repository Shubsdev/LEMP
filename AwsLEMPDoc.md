 # LEMP STACK IMPLEMETATION ON AWS 
 
This is a Walkthrough on steps to create a LEMP Stack (Linux, Nginx, MySQL, PHP)

First Step would be to Launch an  EC2 instance on the AWS console.

To install Nginx

    sudo apt update
    sudo apt install nginx

To verify that nginx was successfully installed and is running as a service in Ubuntu
      
    sudo systemctl status nginx

<img width="495" alt="image" src="https://user-images.githubusercontent.com/102925329/201746287-4de8d7bd-e30f-495e-85e1-c3d7b16c1c6a.png">

To confirm we can access it locally : 

        curl http://localhost:80

<img width="494" alt="image" src="https://user-images.githubusercontent.com/102925329/201747020-8d7b0c1a-6afd-491f-ac83-2a92b99d2826.png">

To confirm we can access it from the internet : 

<img width="523" alt="image" src="https://user-images.githubusercontent.com/102925329/201747613-30de52c0-7223-4fbb-943d-e4d6f12577b8.png">


**INSTALLING MYSQL**

We use the command : 
     
    sudo apt install mysql-server
    
Log Into the MYSQL console with the command : 

    sudo mysql
Run this script to lock down database and set password: 

    ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';

Secure Installation

    sudo mysql_secure_installation
    
Test to login into MYSQL console : 

    sudo mysql -p
    

**INSTALLING PHP**

While Apache embeds the PHP interpreter in each request, Nginx requires an external program to handle PHP processing and act as a bridge between the PHP interpreter itself and the web server. This allows for a better overall performance in most PHP-based websites, but it requires additional configuration. You’ll need to install php-fpm, which stands for “PHP fastCGI process manager”, and tell Nginx to pass PHP requests to this software for processing. Additionally, you’ll need php-mysql, a PHP module that allows PHP to communicate with MySQL-based databases. Core PHP packages will automatically be installed as dependencies.

Run : 

      sudo apt install php-fpm php-mysql
      
**Configuring Nginx to Use PHP Processor**

To create the new directory : 

    sudo mkdir /var/www/lempproject

Assign ownership of the directory with your current system user: 

    sudo chown -R $USER:$USER /var/www/lempproject

Then, create and open a new configuration file in Nginx’s sites-available directory using your preferred command-line editor: 

    #/etc/nginx/sites-available/lempproject

    server {
    listen 80;
    server_name lempproject www.lempproject;
    root /var/www/lempproject;

    index index.html index.htm index.php;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
     }

    location ~ /\.ht {
        deny all;
    }

    }

Activate your configuration by linking to the config file from Nginx’s sites-enabled directory:

     sudo ln -s /etc/nginx/sites-available/lempproject /etc/nginx/sites-enabled/
     sudo nginx -t
     
<img width="495" alt="image" src="https://user-images.githubusercontent.com/102925329/201751974-704fccfa-af9c-40c3-8693-2c71b9260c22.png">

We also need to disable default Nginx host that is currently configured to listen on port 80, for this run:

    sudo unlink /etc/nginx/sites-enabled/default
    sudo systemctl reload nginx
Your new website is now active, but the web root /var/www/lempproject is still empty. Create an index.html file in that location so that we can test that your new server block works as expected:

    sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/lempproject/index.html
    
    
Now go to your browser and try to open your website URL using IP address:

<img width="523" alt="image" src="https://user-images.githubusercontent.com/102925329/201753042-3736ed97-7d0c-46d5-a3d8-385f98d9d5c9.png">

**Testing PHP on SERVER**

You can do this by creating a test PHP file in your document root. Open a new file called info.php within your document root in your text editor:

       sudo nano /var/www/lempproject/info.php
 
 Paste this code:
       
       <?php
        phpinfo();
        
  You can now access this page in your web browser by visiting the domain name or public IP address you’ve set up in your Nginx configuration file, followed by /info.php:
  
  <img width="525" alt="image" src="https://user-images.githubusercontent.com/102925329/201762376-42eba92f-03f7-4de1-b9b2-b94338806cf8.png">
