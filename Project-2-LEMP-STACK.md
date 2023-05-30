# darey-projects

## spin up ubuntu servere in aws

## Step 1

### Installing the nginx server
```
sudo apt update
sudo apt install nginx
```

verify that the nginx successfully installed

![verifying nginx](https://user-images.githubusercontent.com/83009045/159641992-3c0610f9-eb40-44fa-941b-86df87c28c57.JPG)

![nginx web page](https://user-images.githubusercontent.com/83009045/159642056-d4949fde-8485-4c9e-bc61-713796abe1a7.JPG)

## Step 2

### Istalling mysql
```
sudo apt install mysql-server
```

Run the security script with this command :
```
sudo mysql_secure_installation
```

Test for mysql with this command :
```
sudo mysql
```

![mysql confirmation](https://user-images.githubusercontent.com/83009045/159643852-2ae389db-8f59-48cd-a455-e87c44c40736.JPG)


## Step 3

### Insallingh php and its dependencies

```
sudo apt install php-fpm php-mysql
```

## Step 4

###  Configuring Nginx to Use PHP Processor

Create the root web directory for your_domain as follows:
```
sudo mkdir /var/www/projectLEMP
```

Next, assign ownership of the directory with the $USER environment variable, which will reference your current system user:
```
sudo chown -R $USER:$USER /var/www/projectLEMP
```

Then, open a new configuration file in Nginx’s sites-available directory using your preferred command-line editor. Here, we’ll use nano:
```
sudo nano /etc/nginx/sites-available/projectLEMP
```

Paste the following bare bone command
```
#/etc/nginx/sites-available/projectLEMP

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
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
     }

    location ~ /\.ht {
        deny all;
    }

}
```

Activate your configuration by linking to the config file from Nginx’s sites-enabled directory: This will tell Nginx to use the configuration next time it is reloaded.
```
sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/
```

You can test your configuration for syntax errors by typing:
```
sudo nginx -t
```

![nginx syntax](https://user-images.githubusercontent.com/83009045/159648380-abe5d558-6bee-49c6-84a7-7a2c88d96720.JPG)

We also need to disable default Nginx host that is currently configured to listen on port 80, for this run:
```
sudo unlink /etc/nginx/sites-enabled/default
```

When you are ready, reload Nginx to apply the changes:
```
sudo systemctl reload nginx
```

The new website is now active, but the web root /var/www/projectLEMP is still empty. We will create an index.html file in that location so that we can test that our new server block works as expected:
```
sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html
```

![index html file on my browser](https://user-images.githubusercontent.com/83009045/159648444-3c57da27-4940-441d-bda4-c7063be36727.JPG)


## Step 5


### Testing PHP with Nginx
```
sudo nano /var/www/projectLEMP/info.php
```

and paste the following configuration

```
<?php
phpinfo();
```

Access this page in your web browser by visiting the domain name or public IP address you’ve set up in your Nginx configuration file, followed by /info.php:

http://`server_domain_or_IP`/info.php

![php page](https://user-images.githubusercontent.com/83009045/159653235-6cfda00f-9df9-4dd6-93f6-32f9eb682453.JPG)

Its advisable you remove this page after checking because it contains sensitive information about your server. To remove use this command:
```
sudo rm /var/www/projectLEMP/info.php
```

## Step 6
 
Retrieving data from MySQL database with PHP

We will create a database named **onyeka_database** and a user named **onyeka_user**, but you can replace these names with different values.

First, connect to the MySQL console using the root account: with this command

```
sudo mysql
```

To create a new database use this command:

```
CREATE DATABASE onyeka_database;
```

Now you can create a new user and grant him full privileges on the database you have just created.

The following command creates a new user named onyeka_user, using mysql_native_password as default authentication method. We’re defining this user’s password as onyeka12345

```
CREATE USER 'onyeka_user'@'%' IDENTIFIED WITH mysql_native_password BY 'onyeka12345';
```

Now we need to give this user permission over the onyeka_database database:

```
GRANT ALL ON onyeka_database.* TO 'onyeka_user'@'%';
```

You can test if the new user has the proper permissions by logging out and in to the MySQL console again, this time using the custom user credentials:

```
mysql -u onyeka_user -p
```

![show database](https://user-images.githubusercontent.com/83009045/159675092-493b74c0-ea44-45dc-8c90-1e182c006058.JPG)

Next, we’ll create a test table named **todo_list**. From the MySQL console, run the following statement:

```
CREATE TABLE onyeka_database.todo_list (item_id INT AUTO_INCREMENT, content VARCHAR(255), PRIMARY KEY(item_id));
```

Insert a few rows of content in the test table. You might want to repeat the next command a few times, using different VALUES:

```
INSERT INTO onyeka_database.todo_list (content) VALUES ("My first important item");
```

To confirm that the data was successfully saved to your table, run:

```
SELECT * FROM onyeka_database.todo_list;
```

![todo list](https://user-images.githubusercontent.com/83009045/159676549-91b29db7-cb90-4c10-97ae-a33e9380a1bf.JPG)

Exit from mysql

Now you can create a PHP script that will connect to MySQL and query for your content. Create a new PHP file in your custom web root directory.

```
nano /var/www/projectLEMP/todo_list.php
```

The following PHP script connects to the MySQL database and queries for the content of the todo_list table, displays the results in a list. copy it to your todo_list.php

```
<?php
$user = "onyeka_user";
$password = "onyeka12345";
$database = "onyeka_database";
$table = "todo_list";

try {
  $db = new PDO("mysql:host=localhost;dbname=$database", $user, $password);
  echo "<h2>TODO</h2><ol>";
  foreach($db->query("SELECT content FROM $table") as $row) {
    echo "<li>" . $row['content'] . "</li>";
  }
  echo "</ol>";
} catch (PDOException $e) {
    print "Error!: " . $e->getMessage() . "<br/>";
    die();
}
```

You can now access this page in your web browser by visiting the domain name or public IP address configured for your website, followed by /todo_list.php:

```
http://<Public_domain_or_IP>/todo_list.php
```

![browser todo list](https://user-images.githubusercontent.com/83009045/159680580-2c3321ee-50f0-44a1-9d3c-3a7354797776.JPG)

My PHP environment is ready to connect and interact with your MySQL serve

### End of Project 2
