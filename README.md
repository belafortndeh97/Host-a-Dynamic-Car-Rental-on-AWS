Host-a-Dynamic-Car-Rental-on-AWS
# DevOps Project: Dynamic Car Rental Application Deployment on AWS

## Overview

This project involves deploying a dynamic car rental application on AWS, utilizing various services and resources for enhanced reliability, fault tolerance, and scalability. Below is an overview of the key components and steps involved in the deployment.

## Architecture Overview

![Architecture Diagram](link-to-your-diagram)

1. **Virtual Private Cloud (VPC) Configuration**
   - Configured a VPC with public and private subnets across two availability zones for enhanced reliability.
   - Deployed an Internet Gateway to facilitate connectivity between VPC instances and the wider Internet.

2. **Public and Private Subnets**
   - Utilized public subnets for resources like Nat Gateway, Bastion Host, and Application Load Balancer.
   - Associated the public route table with public subnets for internet access through the Internet Gateway.
   - Placed web servers and database servers in private subnets for security.

3. **S3 Bucket for Web Files**
   - Created an S3 bucket and uploaded web files to it.

4. **IAM Role for EC2 Instance**
   - Created an IAM Role with the S3FullAccess policy to allow EC2 instances to download web files from the S3 bucket.

5. **EC2 Instances**
   - Hosted the website on EC2 instances.
   - Utilized EBS volumes for EC2 instance storage.
   - Configured a Setup Server to install the website and import SQL data into the database.

6. **RDS for Database**
   - Used RDS for the database, enhancing scalability and manageability.
   - Imported SQL data into the RDS database using a Setup Server and MySQL Workbench.

7. **AMI Creation and EC2 Auto Scaling**
   - Created an AMI from the configured EC2 instance.
   - Launched new EC2 instances from the AMI using Auto Scaling for availability and scalability.

8. **Application Load Balancer (ALB) and Auto Scaling**
   - Employed an ALB and a target group to distribute web traffic evenly to an Auto Scaling Group across multiple availability zones.
   - Used Auto Scaling to manage EC2 instances automatically, ensuring availability, fault tolerance, and elasticity.

9. **Certificate Manager for Security**
   - Secured application communications using Certificate Manager.

10. **Simple Notification Service (SNS) Integration**
    - Integrated SNS to receive alerts about activities within the Auto Scaling Group.

11. **Domain Registration and DNS Setup**
    - Registered a domain name and set up DNS records using Route 53.

12. **Version Control on GitHub**
    - Stored web files on GitHub for version control and collaboration.

## Deployment Scripts

### EC2 Instance Setup Script

```bash
# Script to setup EC2 instance for web application

# Update EC2 instance
sudo yum update -y

# Install Apache
sudo yum install -y httpd
sudo systemctl enable httpd
sudo systemctl start httpd

# Install PHP 7.4
sudo amazon-linux-extras enable php7.4
sudo yum clean metadata
sudo yum install php php-common php-pear -y
sudo yum install php-{cgi,curl,mbstring,gd,mysqlnd,gettext,json,xml,fpm,intl,zip} -y

# Install MySQL 5.7
sudo rpm -Uvh https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2022
sudo yum install mysql-community-server -y
sudo systemctl enable mysqld
sudo systemctl start mysqld

# Download web files from S3
sudo aws s3 sync s3://redzone-web-files /var/www/html

# Unzip and move files
cd /var/www/html
sudo unzip rentzone.zip
sudo mv rentzone/* /var/www/html
sudo mv rentzone/.well-known /var/www/html
sudo mv rentzone/.env /var/www/html
sudo mv rentzone/.htaccess /var/www/html
sudo rm -rf rentzone rentzone.zip

# Enable mod_rewrite
sudo sed -i '/<Directory "\/var\/www\/html">/,/<\/Directory>/ s/AllowOverride None/AllowOverride All/' /etc/httpd/conf/httpd.conf

# Set permissions
sudo chmod -R 777 /var/www/html
sudo chmod -R 777 storage/

# Add database credentials
sudo vi .env

# Restart server
sudo service httpd restart
```

### SSH into Instance in Private Subnet with PowerShell

```powershell
# PowerShell script to SSH into instance in private subnet

# Check if the ssh-agent is running
Get-Service ssh-agent

# Start the service
Start-Service ssh-agent

# Load your key files into the ssh-agent
ssh-add C:\Users\Admin\my-ec2key.pem

# SSH into the instance (bastion host) in the public subnet. Remember to allow agent forwarding
```

Feel free to customize the scripts according to your environment and requirements.
