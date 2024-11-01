![Alt text](/2._Host_a_WordPress_Website_on_AWS.png)

---

# WordPress Deployment on AWS using EC2 and Other AWS Services

This project demonstrates hosting a WordPress website on AWS infrastructure using various AWS services for improved scalability, fault tolerance, and security. The repository contains the necessary configuration files and scripts to set up the environment and deploy WordPress on EC2 instances within a secure, multi-availability zone architecture.

## Architecture Overview

The WordPress application is deployed within a custom Virtual Private Cloud (VPC), with public and private subnets across two availability zones to improve availability and fault tolerance. The architecture includes:

- VPC: A Virtual Private Cloud with both public and private subnets distributed across two Availability Zones.
- Internet Gateway: Provides internet access for instances within public subnets.
- Security Groups: Act as firewalls to control inbound and outbound traffic.
- EC2 Instances: Host the WordPress website on instances within private subnets.
- Application Load Balancer (ALB): Distributes incoming traffic across multiple EC2 instances for load balancing.
- Auto Scaling Group (ASG)**: Automatically scales the number of EC2 instances based on traffic, ensuring availability and elasticity.
- EFS: Elastic File System for shared file storage across multiple EC2 instances.
- RDS: Amazon Relational Database Service for the MySQL database.
- Route 53: For DNS management and domain name registration.
- SNS: Simple Notification Service for monitoring and alerting on ASG activity.
- Certificate Manager: For securing application communication via HTTPS.

## Prerequisites

Ensure you have the following before deploying:
- AWS CLI configured with appropriate permissions.
- IAM role for EC2 with permissions for EFS, RDS, and S3 access.
- A registered domain name in Route 53.

## Project Structure

- `scripts/`: Contains installation and configuration scripts for EC2 instances and Auto Scaling Groups.
- `diagrams/`: Reference architecture diagram for the project setup.
- `launch-template.sh`: Bash script for setting up EC2 instances in the Auto Scaling Group.
- `install-wordpress.sh`: Bash script to install WordPress and required dependencies.

## Deployment Steps

### 1. Setup VPC and Networking Components
- Create a VPC with both public and private subnets across two Availability Zones.
- Attach an Internet Gateway to the VPC for external connectivity.
- Set up NAT Gateways in the public subnets to allow private instances to access the Internet.

### 2. Configure EC2 Instances
- Launch EC2 instances in the private subnets for improved security.
- Use the provided `install-wordpress.sh` script to install Apache, PHP, and MySQL on each instance.
- Mount the EFS volume on each EC2 instance to share files across the WordPress application.

### 3. Load Balancer and Auto Scaling Configuration
- Set up an Application Load Balancer in the public subnets to distribute traffic across EC2 instances.
- Configure an Auto Scaling Group (ASG) using the `launch-template.sh` script for automated instance scaling.
- Enable monitoring and alerts using SNS to notify about scaling activities.

### 4. Database and Storage Setup
- Deploy an RDS instance to handle the WordPress MySQL database.
- Configure EFS for shared storage of WordPress files across EC2 instances.

### 5. DNS and SSL
- Use Route 53 to configure DNS for your domain and map it to the Application Load Balancer.
- Use AWS Certificate Manager to obtain an SSL certificate for secure HTTPS connections.

## Installation and Configuration Scripts

### WordPress Installation Script

Use this script (`scripts/install-wordpress.sh`) to configure each EC2 instance in the private subnet:
```bash
# Switch to root user
sudo su

# Update system packages
sudo yum update -y

# Setup Apache web server and start
sudo yum install -y httpd
sudo systemctl enable httpd
sudo systemctl start httpd

# Install PHP and dependencies
sudo dnf install -y php php-mbstring php-gd php-mysqlnd ...

# Download and setup WordPress
wget https://wordpress.org/latest.tar.gz
tar -xzf latest.tar.gz
sudo cp -r wordpress/* /var/www/html/

# Set permissions
chown apache:apache -R /var/www/html

# Restart Apache
sudo systemctl restart httpd
```

### Auto Scaling Group Launch Template

The launch template script (`scripts/launch-template.sh`) will automatically configure the EC2 instances as they are created:
```bash
#!/bin/bash
sudo yum update -y
sudo yum install -y httpd
sudo systemctl enable httpd
sudo systemctl start httpd

# Mount EFS
echo "$EFS_DNS_NAME:/ /var/www/html nfs4 ..." >> /etc/fstab
mount -a
```

## Monitoring and Alerts

- Configure Amazon CloudWatch for monitoring metrics.
- Use SNS to receive notifications related to Auto Scaling Group activity.

## License

This project is licensed under the MIT License.

## Acknowledgments

Thanks to AWS documentation and online resources for guidance on deploying scalable WordPress solutions on AWS.

---
