# Hosting a Django Project with PostgreSQL on AWS EC2 with Nginx
This guide will walk you through the steps of hosting a Django project with PostgreSQL as the database on an AWS EC2 instance, and configuring Nginx as the server to serve the Django application.

# Step 1: Creating an EC2 instance in AWS
### Sign in to AWS Console:
- Create an  AWS account at https://aws.amazon.com/.
### Launch an Instance
- Look for EC2 using the search bar.
- On the EC2 dashboard, choose the instances or simply click on "Launch Instance."
- To create a new instance, click on "Launch Instance."
- In the "Name and tags" section, enter your project name (optional).
- Choose an operating system from the Application and OS Images section, like Ubuntu or Linux.
- Select an instance type that aligns with the requirements of your project.
- Here we're configuring an EC2 instance with the Ubuntu 64-bit operating system and the t3.micro instance type, which qualifies for the AWS Free Tier.
- Generate and Download Your PEM File: To establish a secure connection with your EC2 instance, you'll require a PEM (Privacy Enhanced Mail) file containing your private key. Give the PEM file your project name.
