<h1>Introduction:</h1>

For Project 1 the class was asked to install WordPress via our Google Cloud virtual machine. This was to be done manually to allow us to know more of the ins and outs of creating a working webpage. The components needed for the webpage to be functional include PHP, Apache2, and MariaDB. PHP is a computer language used to allow for server-side programming and communication. For PHP to work smoothly it must be configured to work with the HTTP server, Apache2. Apache2 is an HTTP server that allows other users to access certain files on the server with a proper server and internet connection, via a web browser. MariaDB on the other hand, is an open-source and secure database management software. It allows for large amounts of data to be displayed on a webpage and for tables to be created with ease. All of these components, MariaDB, PHP, and Apache2 create what is called a LAMP stack. A LAMP stack stands for Linux, Apache, MySQL, and PHP. All of these components allow users to create, manage, and host a website. 

<h1>Installing and Configuring the LAMP Stack:</h1>

<h1>Apache2:</h1>

The first step to installing WordPress is to make sure the Apache Web Server is configured correctly. In order to do this we must update our system with commands: `sudo apt update` and `sudo apt -y upgrade`. These commands allow us to install any updates needed in order to install new content correctly and insure that everything will be compatible, which is easier to troublshoot. Next we must identify the specific package name that we want to install by using the command: `sudo apt search apache2 | head`. This allows us to see multiple packages that we can install and eventually find the one we need. For this project we want apache2, so we use command: `apt show apache2` in order to examine that specific package. Once we have comfirmed that we examined the correct package we must install, so we use command: `sudo apt install apache2`. Once the package is installed we must check that it was installed correctly by using command: `systemctl list-unit-files apache2.service` and `systemctl status apache2`. 

<h1>PHP:</h1>

After setting up the web server, Apache2, we must install and configure PHP so that we can change or update the webpage at any time. It must also work with apache2 so that everything is compatible and dynamic. PHP will specifically communicate with the server, apache2, and databases that are created using MariaDB or MySQL. In order to install PHP we must use the `apt` command and restart the web server simultaneously by using commands: `sudo apt install php libapache2-mod-php` and `sudo systemctl restart apache2`. Once this is done we must check if the installation was successful by using command: `systemctl status apache2`. The commands to install PHP are pretty straight-forward, but in order to properly check if it was successful we can create a file called "info.php" in the directory "cd /var/www/html/". The command to create this file in the proper directory is: "cd /var/www/html/" and "sudo nano info.php".
Once we get to the new file screen we can add the following data: 

`<?php
phpinfo();
?>`

We must save this file and check the output via our external IP address for our server. We can use any internet browser to look up our IP address and visit it. If we want to comfirm what our external IP address is we can check via the google cloud console. We must navigate to our virtual machine instance and look in the "details" section and scroll until we see "Network Interfaces". Here we will see our external IP address and be able to look it up via our web browser. If PHP was installed successfully we will see the system information of the PHP install page because of the file, "info.php", that we created in order to check the installation via our external IP address. Once the installation is confirmed, we must configure a file called "index.php" that will be found by apache2 and default to that file when using our external IP address. This is because apache2 will serve files in the order that we code it for. The default file that is served by apache2 first is "index.html", but since we are using php we must have apache2 default to serve "index.php" first. The first commands that we must use are: `cd /etc/apache2/mods-enabled/` and `sudo nano dir.conf`. These commands allow us to edit the "dir.conf" file that determines what file apache2 defaults to. We must change the line in the file to the following: 

`DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm`

This will allow apache2 to default to our new file "index.php". In order to check if the change was successful, we must use the following command: `apachectl configtest`. If we get a "syntax ok" message we can reload apache2 with the correct configuration with the following commands: 

`sudo systemctl reload apache2` and `sudo systemctl restart apache2`.

We must now create the index.php file, using commands: `cd /var/www/html/` and `sudo nano index.php`.

Within this file we must edit it and copy this code to it and save: 

`<html>
<head>
<title>Broswer Detector</title>
</head>
<body>
<p>You are using the following browser to view this site:</p>

<?php
echo $_SERVER['HTTP_USER_AGENT'] . "\n\n";

$browser = get_browser(null, true);
print_r($browser);
?>
</body>
</html>`

To check if the changes were made correctly we can navigate to our external IP address. 

<h1>MariaDB:</h1>

In order to create databases and have it reflect on our webpage we must install and configure MariaDB. The first step is to use the `apt` command to install the MariaDB Community Server and then log into the MariaDB shell under the MariaDB root account using the following command: `sudo apt install mariadb-server mariadb-client`. In order to check if the installation was successful we can use the systemctl command: `systemctl status mariadb`. Once we confirm the install was successful we must run a post installation script that allows us to set the MariaDB root password. The script is: `sudo mysql_secure_installation`. This script will prompt us to answer a few configuration steps. We must not rush past this step and follow it verbatum. The following answers should match the following: 

Enter the current password for root (enter for none):
Set root password: Y
New Password: XXXXXXXXX (create one that you will remember)
Re-enter new password: XXXXXXXXX (see above text)
Remove anonymous users: Y
Disallow root login remotely: Y
Remove test database and access to it: Y
Reload privilege tables now: Y

Once these prompts have been answered we can login to the database to test it using command: "sudo su". Once we are root we must run the "show databases;" command. \q is the key to exit. We must now create a regular user and save the root for special cases, use the following command: MariaDB [(none)]> create user 'webapp'@'localhost' identified by 'XXXXXXXXX';". If "Query OK" appears then a new user has been created. The last steps are to insure that PHP and MySQL communication is supported, to do this we use the following command: "sudo apt install php-mysql". We can now restart apache2 and MariaDB with commands: "sudo systemctl restart apache2" and "sudo systemctl restart mariadb". Next we need to create a login.php file in the /var/www/html directory in order for PHP to connect to MariaDB. In doing so we need to change group ownership of the file so apache2 can read it. The following commands will help us with this: 

`cd /var/www/html/
sudo touch login.php
sudo chmod 640 login.php
sudo chown :www-data login.php
ls -l login.php
sudo nano login.php`

We must also add this to the file:

`<?php // login.php
$db_hostname = "localhost";
$db_database = "linuxdb";
$db_username = "webapp";
$db_password = "XXXXXXXXX"; \\use your own password for "X's"
?>`

After these steps are completed make sure to save the file and exit. We must now create a PHP file that will display HTML but also use PHP to interact with our MariaDB distributions database. Use the following command: `sudo nano distros.php`. Now copy the following code: 

`<html>
<head>
<title>MySQL Server Example</title>
</head>
<body>

<?php

// Load MySQL credentials
require_once 'login.php';

// Establish connection
$conn = mysqli_connect($db_hostname, $db_username, $db_password) or
  die("Unable to connect");

// Open database
mysqli_select_db($conn, $db_database) or
  die("Could not open database '$db_database'");

// QUERY 1
$query1 = "show tables from $db_database";
$result1 = mysqli_query($conn, $query1);

$tblcnt = 0;
while($tbl = mysqli_fetch_array($result1)) {
  $tblcnt++;
}

if (!$tblcnt) {
  echo "<p>There are no tables</p>\n";
}
else {
  echo "<p>There are $tblcnt tables</p>\n";
}

// Free result1 set
mysqli_free_result($result1);

// QUERY 2
$query2 = "select name, developer from distributions";
$result2 = mysqli_query($conn, $query2);

$row = mysqli_fetch_array($result2, MYSQLI_NUM);
printf ("%s (%s)\n", $row[0], $row[1]);
echo "<br/>";

$row = mysqli_fetch_array($result2, MYSQLI_ASSOC);
printf ("%s (%s)\n", $row["name"], $row["developer"]);

// Free result2 set
mysqli_free_result($result2);

// Query 3
$query3 = "select * from distributions";
$result3 = mysqli_query($conn, $query3);

while($row = $result3->fetch_assoc()) {
  echo "<p>Owner " . $row["developer"] . " manages distribution " . $row["name"] . ".</p>";
}

mysqli_free_result($result3);

$result4 = mysqli_query($conn, $query3);
while($row = $result4->fetch_assoc()) {
  echo "<p>Distribution " . $row["name"] . " was released on " . $row["founded"] . ".</p>";
}

// Free result4 set
mysqli_free_result($result4);

/* Close connection */
mysqli_close($conn);

?>

</body>
</html>`

Save the file and exit. 

To test the PHP syntax we must run the following commands: `sudo php -f login.php` and `sudo php -f distros.php`. If there are any errors the commands will show us which lines have errors, and if it is successful nothing should be outputted for the first command. If the second command is successful then HTML will be outputted. 

Finally, since the LAMP Stack has been created, we can follow the steps to installing WordPress. 

<h1>WordPress:</h1>

WordPress is a free and open source Content Management System (CMS). It allows users to create websites and edit specific components. It also supports other website content such as blogs, mail lists, media galleries, etc. It powers 43.3 percent of all of the internet's websites. We created the LAMP Stack in order to have more control and customization of our WordPress website, allowing us to store lots of information and change certain components that would be harder to do so with a default install of WordPress. If any users want to know more about WordPress or how to install it then they can visit the website provided: https://developer.wordpress.org/advanced-administration/before-install/howto-install/

The first step is to make sure that we have the proper versions of PHP and MariaDB, we can do this with the following commands: "php --version" and "mariadb --version". We must have at least PHP version 7.4, MySQL version 5.7, and/or MariaDB version 10.2 or higher. Next we must install some PHP modules that allow WordPress to function at its fullest, we will use the following command: `sudo apt install php-curl php-xml php-imagick php-mbstring php-zip php-intl`. We must now restart Apache2 and MariaDB with these commands: `sudo systemctl restart apache2` and `sudo systemctl restart mariadb`. Next we will download and extract the WordPress software. We must do so within the "cd /var/www/html" directory. Also, we must make sure we are downloading the latest version of WordPress with the `wget` command and extract with the `tar` command:
`cd /var/www/html`, `sudo wget https://wordpress.org/latest.tar.gz`, and `sudo tar -xzvf latest.tar.gz`. Once the WordPress software has been downloaded and extracted, we need to create a WordPress database within MariaDB by logging into the root Linux user and logging into the MariaDB root user. We will use the following commands to do so: `sudo su" and "mariadb -u root`. These commands will put us in the MariaDB command prompt where we must enter information to create our WordPress database and user. We will enter the following information into the prompt:

`create user 'wordpress'@'localhost' identified by 'XXXXXXXXX'; (create your own password)
create database wordpress;
grant all privileges on wordpress.* to 'wordpress'@'localhost';
show databases;
\q`

Once we have created the WordPress database and the login credentials we need to create a "wp-config.php" file. This file will store the name of the database, User, and Password for the user. We can create this file with the following commands: 

`cd /var/www/html/wordpress
sudo cp wp-config-sample.php wp-config.php
sudo nano wp-config.php`

In nano we must fill in the correct information about our database name, user, and password. We also want to disable FTP uploads on this site, so we must add the following line to the file and save: `define('FS_METHOD','direct');`.

Next we must change file ownership with the `chown` command: `sudo chown -R www-data:www-data *`. You will need to be in your base directory to run this command, such as "/var/www/html/wordpress".

Finally we want to run the WordPress install script within a web browser of our choice and with our external IP address of our Virtual Machine included: 
http://11.111.111.11/wordpress/wp-admin/install.php (fill in your external IP address for the 1's)

This script, when run with your external IP address in a web browser, will return official WordPress documentation and prompts to create your first webpage!

Congratulations! Using the LAMP Stack and the WordPress guide, we were able to create a dynamic webpage as a host and customize it to our liking. All of these steps help ensure that our website works properly and allows us more privileges than a default download of WordPress. 
