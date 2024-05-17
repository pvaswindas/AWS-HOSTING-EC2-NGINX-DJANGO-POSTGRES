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
- Here we are configuring an EC2 instance with Ubuntu 64-bit OS and t3.micro type, eligible for AWS Free Tier.
- Create and download your PEM file from the "Key pair" section, giving it your project name.
- Verify EC2 instance network settings and permissions: Ensure "Allow SSH," "Allow HTTPS," and "Allow HTTP" are enabled. Then, click "Launch Instance."

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

### Access the instance by logging in:
- Now, paste the command copied earlier from the SSH client tab.
- After pasting the command, you may encounter a prompt about host authenticity. Type "yes" and press Enter to continue connecting.

### Installing required dependencies:
- sudo apt update
- sudo apt install python3-pip python3-dev libpq-dev postgresql postgresql-contrib nginx curl

# Step 3: Setting Up Database in Postgres
- Connect to postgres   :   sudo -u postgres psql
- Create database   :   CREATE DATABASE project_name;
- Create user   :   CREATE USER user_name WITH PASSWORD 'password';
- Grant permissions   :   GRANT ALL PRIVILEGES ON DATABASE project_name TO user_name;
- Exit postgres   :   \q

# Step 4: PIP Installation & Git Cloning
- Install pip    :   sudo apt install python3-pip
- Install virtual environment   :   sudo apt install python3.12-venv
- Create a virtual env   :   python3 -m venv env
- Confirm environment creation   :   ls
- Activate virtual env   :   source env/bin/activate
- Install psycopg2-binary   :   pip install psycopg2-binary
- Clone the git repository   :   git clone https://github.com/octocat/Spoon-Knife
- Confirm git cloning   :   ls

