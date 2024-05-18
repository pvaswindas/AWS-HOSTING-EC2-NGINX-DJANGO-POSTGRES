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
```bash
- sudo apt update
```
```bash
- sudo apt install python3-pip python3-dev libpq-dev postgresql postgresql-contrib nginx curl
```

# Step 3: Setting Up Database in Postgres
- Connect to postgres   :
  ```bash
  sudo -u postgres psql
  ```
- Create database   :

  ```bash
  CREATE DATABASE project_name;
  ```
- Create user   :

  ```bash
  CREATE USER user_name WITH PASSWORD 'password';
  ```
- Grant permissions   :

  ```bash
  GRANT ALL PRIVILEGES ON DATABASE project_name TO user_name;
  ```
  ```bash
  GRANT USAGE ON SCHEMA schema_name TO username;
  ```
- Exit postgres   :

  ```bash
  \q
  ```


# Step 4: PIP Installation & Git Cloning
### » Install pip
```bash
sudo apt install python3-pip
```
### » Install virtual environment
```bash
sudo apt install python3.12-venv
```
### » Create a virtual env
```bash
python3 -m venv env
```
### » Activate virtual env
```bash
source env/bin/activate
```
### » Install psycopg2-binary
```bash
pip install psycopg2-binary
```
### » Clone the git repository
##### Replace `<your-git-repository-url>` with the URL of your Git repository.
```bash
git clone <your-git-repository-url>
```


# Step 5: Configure settings.py file
- Navigate to the project's root directory containing the manage.py file.
- Helpful commands :
  - "ls" to list all directories present in this file.
  - "pwd" to display the current directory's path.
  - "cd" to navigate to different directories. For example: "cd project_file_name".
- Navigate to the project's root directory using cd
- Open settings.py file in an editor : sudo nano settings.py
- 
### Configure the database with the details previously created on PostgreSQL : 
##### Replace `database_name`, `user_name`, and `user_password` with the details previously created.
```bash
DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.postgresql_psycopg2",
        "NAME": "database_name",
        "USER": "user_name",
        "PASSWORD": "user_password",
        "HOST": "localhost",
        "PORT": "",
    }
}
```

### Also make changes in the static configuration
```bash
BASE_DIR = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))
STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
STATIC_URL = '/static/'
STATICFILES_DIRS = [
  os.path.join(BASE_DIR, 'static'),
]
```

# Step 6: Dependencies, Migration, Superuser, Static
### Install Requirements
###### Navigate to your project's root directory and execute this command :
```bash
pip install -r requirements.txt 
```
###### You have to create requirements.txt in your root directory before using this command.

##### Verify the installation :
```bash
pip freeze
```
### Migrate
##### Comment out the urls :
- Navigate into the main directory of your project, where settings.py is located, using the "cd" command.
- Open the `url.py` file in the main directory of project using the nano editor :
  
  ```bash
  sudo nano urls.py
  ```
###### We need to comment out all the URLs except for admin.site.urls.
  ```bash
  urlpatterns = [
    path("admin/", admin.site.urls),
    # path("", include("user.urls")),
    # path("", include("admin.urls")),
    # path("accounts/", include("allauth.urls"), name="accounts"),
  ]
  ```
###### We can revert this action after completing the migrations; otherwise, it may cause errors when executing the migration commands.

##### Run Migration commands in the same order :

```bash
python manage.py migrate
```

```bash
python manage.py makemigrations
```

###### Now uncomment the URLs that were previously commented.

##### Create Superuser :
```bash
python manage.py createsuperuser
```

##### Collect Static Files :
```bash
python manage.py collectstatic
```
