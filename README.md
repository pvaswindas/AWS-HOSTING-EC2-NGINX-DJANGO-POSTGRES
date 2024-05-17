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
- Create and Download Your PEM File from the "Key pair" section: To establish a secure connection with your EC2 instance, you'll require a PEM (Privacy Enhanced Mail) file containing your private key. Give the PEM file your project name.
- Configuring Network Settings and Permissions: Now, let's verify that your EC2 instance has the required network settings and permissions to communicate externally. Ensure that "Allow SSH traffic from," "Allow HTTPS traffic from the internet," and "Allow HTTP traffic from the internet" are enabled or selected.
-  Now, proceed by clicking on "Launch Instance."

# Step 2: Establishing Connection with Your EC2 Instance
### Copy the SSH login command:
- On your EC2 Instance Dashboard, choose the instance you've recently created.
- Click on the "Connect" button located at the top right corner.
- Select the SSH client and copy the SSH login command from the Example section (ssh -i key_.pem ec2-user@your-ec2-instance-public-ip).

### Granting permissions to the .pem file:
- Open Command Prompt (or Terminal) in the directory where the .pem file is located.
- Enter the command to change the .pem file permissions, setting it to read-only for the owner:
  - For Linux: "chmod 400 key_name.pem"
  - For Windows: "icacls key_name.pem /inheritance:r /grant:r %USERNAME%:R"
- Make sure to replace "key_name.pem" with the actual name of your .pem file.

