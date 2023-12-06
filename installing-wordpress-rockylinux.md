<h1>Introduction:</h1>

For project 2 we were asked to install WordPress via Google Cloud on another Linux distrubtion system. I decided to try this on Rocky Linux, a free, server-oriented Linux distrubtion system. This version of Linux is compatible with the CentOS ecosystem, which means that it can run most of the software packages of CentOS. CentOS stands for Community Enterprise Operating System, which is a free, open-source, platform that is derived from Redhat Linux. Redhat Linux was discontinued in 2004, but was succeeded by Red Hat Enterprise Linux (RHEL). I wanted to try this version of Linux because when I researched it, Rocky Linux had a quote that said they are "self-imposed not-for-profit" which I think is really humble and interesting. I also chose this version of Linux because I could install it using MariaDB just like project 1. 

<h1>Step 1: Create a virtual Machine</h1>

In order to install a new Linux distrubtion, we must create a new virtual machine within Google Cloud. 

1) First we go to the Google Cloud console
2) Now create a new project 
3) Via the navigation menu on the left, you will go to Compute Engine
4) Create a new instance
	- Click on the "Create" button
	- Choose an instance name, region, and zone
	- Choose "Rocky Linux" for the operating system
	- Configure the boot disk (size and type), networking options, and machine type
5) Configure the firewall rules
	- Make sure to allow HTTP (80) and HTTPS (443)
	- Allowing port 80 means that information between the browser and the server will be plain text, while port 443 will encrypt the information, making it more secure and more widely used.
6) Click the "Create" button to create your VM instance

<h1>Step 2: Connect to your VM</h1>
	- After you have created the VM, you can now press the "SSH" button to open a new terminal in your browser

<h1>Step 3: Install a LAMP Stack (Linux, Apache, MySQL, PHP)</h1>

1) Update your Virtual Machine instance
	- use command: `sudo dnf update`

2) Install Apache
	- use command: `sudo dnf install httpd`

3) Start Apache and enable it on boot
   	- This allows Apache to start as soon as you turn on your virtual machine
	- use commands: 
					`sudo systemctl start httpd
					sudo systemctl enable httpd`

5) Install MariaDB
	- use command: `sudo dnf install mariadb-server`

6) Start MariaDB and enable it on boot
	- use commands: 
					`sudo systemctl start mariadb
					sudo systemctl enable mariadb`

7) Secure MariaDB installation
	- use command: `sudo mysql_secure_installation`

8) Install PHP	
	- use command: `sudo dnf install php php-mysqlnd`

9) Restart Apache
	- use command: `sudo systemctl restart httpd`

<h1>Step 4: Install WordPress</h1>

1) Download and Extract WordPress
	- use commands:  	
					`wget https://wordpress.org/latest.tar.gz
					tar -xvzf latest.tar.gz`
							
2) Move WordPress files to your Apache document root
	- use command: `sudo mv wordpress/* /var/www/html/`

3) Set permissions
	- use command: `sudo chown -R apache:apache /var/www/html/`

4) Create a MySQL database and user for WordPress
	- use commands: 	
					`mysql -u root -p`
					
					`CREATE DATABASE wordpress;
					CREATE USER 'wordpress_user'@'localhost' IDENTIFIED BY 'your_password';
					GRANT ALL PRIVILEGES ON wordpress.* TO 'wordpress_user'@'localhost';
					FLUSH PRIVILEGES;
					EXIT;`

5) Configure WordPress
	- Using your VM's external IP address, use a browser of your choice and type it into the search bar
	- Complete the WordPress setup by entering the database information
	
6) Complete your WordPress installation
	- Follow the on-screen prompts and complete the installation
	




