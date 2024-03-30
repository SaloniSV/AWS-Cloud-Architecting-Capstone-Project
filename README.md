As the final step of the AWS Academy at Digital Futures, I was required to provide a solution for the AWS Academy Cloud Architecting 2.x — Capstone Project. I am summarising the approach I used and the other options that might be used to complete this project. The project deliverables include deploying a PHP application that meets the following requirements:

Deploy a PHP application that runs on an Amazon Elastic Compute Cloud (Amazon EC2) instance
Create a database instance that the PHP application can query
Create a MySQL database from a structured query language (SQL) dump file
Update application parameters in an AWS Systems Manager Parameter Store
Secure the application to prevent public access to backend systems
If you inspect the lab architecture at the beginning, you will notice that the following assets are already provided:

Example VPC
4 subnets, 2 private with route tables configured for connecting to resources within the VPC and 2 public subnets with route to the IGW
4 Security Groups, one for the app, one of the database, one for the application load balancer and one for the bastion host
Bastion Host EC2 instance
Launch configuration with the application assets already installed
Simple solution

The simplest way to meet the project’s requirement is with these steps:

Create a MySQL RDS database instance
Creating a load balancer and autoscaling group
Creating an instance from the launch template provided
Connect to the instance through ssh via bastion host and then connect to the private instance
Transfer the database assets to RDS via SSH
Configure the system parameters through Parameter Store in Systems Manager
Finally, test the website through the load balancer url.
This is a simple solution and is perfectly acceptable for the project. However, in order to provide a more robust environment, and utilising Cloud9 IDE can be add more nuance to your architecture.

A better solution

Use Cloud9 IDE to fetch the project assets required to host the website.
2. Install and update all required plugins on the IDE. I used LAMP stack.

sudo yum -y update
sudo amazon-linux-extras install -y lamp-mariadb10.2-php7.2 php7.2
sudo yum install -y httpd mariadb-server
3. Start and enable the httpd server.

sudo systemctl start httpd
sudo systemctl enable httpd
sudo systemctl is-enabled httpd
4. You will need to temporarily allow internet access to your IDE in order to view the website and verify if it is working properly.


RDS Security Group
5. Unzip the application assets to a folder with path /var/www/html. Check if the website is working. You will not be able to query your website yet as you have yet to create your database.

sudo unzip Example.zip -d /var/www/html/
6. For the database, I used MySQL for RDS and configured it with an attached subnet group in the private subnets of the VPC.


RDS database

RDS Database Configuration
7. After creating the database, I used my Cloud9 IDE to connect to the database and transfer the country data SQL dump file. Check to ensure the data has been transferred.


Query the database
8. Then, create an application load balancer and autoscaling group to provide the website with high availability and scalability.


Application Load Balancer

Listeners configurations

Autoscaling Group configurations
9. Ensure that the app IAM role is attached to the web servers and Cloud9 IDE.

10. Finally, configure the system parameters in the Systems Manager Parameter Store.


Parameters for the Parameter Store
11. Check to see if the website works and can query the database



