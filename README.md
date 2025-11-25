Deploy a scalable and secure WordPress website using Amazon EC2, Amazon RDS (MySQL/MariaDB).

Overview of the Project:

Create a working website for wordpress. We can also download and log in to our wordpress website using the public ipv4 address of the website server. We also can store the data of the wordpress and also can store the data of users and all to a database. This can act as a production ready server.

Architecture Overview:

AWS VPC (Private Network and Subnet) → AWS RDS (MySQL) → AWS EC2 Instance (HTTPD/NGINX/PHP - Wordpress Website Hosting)

Features:

WordPress installed on Amazon EC2 (Ubuntu/AlmaLinux/Amazon Linux).
Database hosted on Amazon RDS.
Secure configuration using IAM roles, security groups.
Automated backups and snapshots.

Prerequisites:
AWS Account
IAM user with access to EC2, RDS, VPC
SSH key pair
VPC with subnets
Installed locally:
AWS CLI
SSH client
Git
Steps:
Configure VPC:
Create VPC in any region (Mumbai)
Mention your specified VPC CIDR blocks
Create other VPC Components (Route Tables, Subnets, etc.)
Configure RDS MySQL:
Engine: MySQL 8.x or MariaDB
Select our own VPC instead of default one
Instance Type: db.t3.micro (free-tier)
Storage: 20GB GP3
Public access: NO
Connect EC2 SG → RDS SG (3306)
 Launch EC2 Instance:
Recommended OS: Amazon Linux 2023
Instance Type: t2.micro (free-tier)
Select our own VPC for the project
Security Group: (80,3306 → from our RDS database Security group,22 → Default)
Connect to the instance from any SSH client and run the commands to install required packages:
sudo yum update -y
sudo yum install -y httpd wget php php-mysqlnd php-fpm php-xml php-gd php-mbstring php-json php-zip
Start & enable services:
sudo systemctl enable httpd
sudo systemctl start httpd
Download and Configure WordPress:
cd /var/www/html
sudo wget https://wordpress.org/latest.tar.gz
sudo tar -xvf latest.tar.gz
sudo mv wordpress/* .
sudo rm -rf wordpress latest.tar.gz
Configure WordPress to connect RDS:
sudo cp wp-config-sample.php wp-config.php
sudo vim wp-config.php
Update DB parameters: (Using the link available in the wp-config.php)
define('DB_NAME', 'wordpress')
define('DB_USER', 'wpuser')
define('DB_PASSWORD', 'strongpassword')
define('DB_HOST', 'rds-endpoint.amazonaws.com')
Set up the document root for HTTPD:
cp -r wordpress/* /var/www/html
cat /var/www/html (Check for the changes and config for wordpress)
Systemctl restart httpd (For config and major changes we need to restart the service itself)
Configure and Connect to the DB Instance:
export MYSQL_HOST=hostendpoint
mysql -h wordpress.cr2i4cmuyziy.ap-south-1.rds.amazonaws.com -P 3306 -u admin -p (Log in using the password for the primary / admin user of the database)
SHOW databases;
USE <database name>;
SHOW tables;
Ping WordPress:
http://<EC2 public IP>


Security Best Practices:
Do NOT allow MySQL port to the internet
Use IAM roles instead of access keys
Enable automatic RDS backups
Enable CloudWatch alarms for CPU, DB connections
