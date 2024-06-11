# Hosting a Django Project with PostgreSQL on AWS EC2 with Nginx

This guide will walk you through the steps of hosting a Django project with PostgreSQL as the database on an AWS EC2 instance, and configuring Nginx as the server to serve the Django application.

## First, read each step completely before executing it.

# Step 1: Creating an EC2 instance in AWS
### Sign in to AWS Console:
- Create an  AWS account at https://aws.amazon.com/.
### Launch an Instance
- Look for EC2 using the search bar.
- On the EC2 dashboard, choose the instances or simply click on "Launch Instance."
- To create a new instance, click on "Launch Instance."
- In the "Name and tags" section, enter your project name (optional).
- Choose an operating system from the Application and OS Images section, `I prefer Ubuntu`.
- Select an instance type that aligns with the requirements of your project.
- Here we are configuring an EC2 instance with Ubuntu 64-bit OS and t3.micro type, eligible for AWS Free Tier.
- Create and download your PEM file from the "Key pair" section, giving it your project name.
  `Store the PEM file in an appropriate directory and do not delete it, as it cannot be recreated.`
- Verify EC2 instance network settings and permissions: Ensure "Allow SSH," "Allow HTTPS," and "Allow HTTP" are enabled. Then, click "Launch Instance."


# Step 2: Establishing Connection with Your EC2 Instance
### Copy the SSH login command:
- On your EC2 Instance Dashboard, choose the instance you've recently created.
- Click on the "Connect" button located at the top right corner.
- Select the SSH client and copy the SSH login command from the Example section (ssh -i key_.pem ec2-user@your-ec2-instance-public-ip).

### Changing permissions to the .pem file:
- Open Command Prompt (or Terminal) in the directory where the .pem file is located.
- Enter the command to change the .pem file permissions, setting it to read-only for the owner:

  `Make sure to replace "key_name.pem" with the actual name of your .pem file.`
  - For Linux: "chmod 400 key_name.pem"
  - For Windows: "icacls key_name.pem /inheritance:r /grant:r %USERNAME%:R"

### Access the instance by logging in:
- Now, paste the command copied earlier from the SSH client tab.
- After pasting the command, you may encounter a prompt about host authenticity. Type "yes" and press Enter to continue connecting.

### Installing required dependencies:
```bash
sudo apt update
```
```bash
sudo apt install python3-pip python3-dev libpq-dev postgresql postgresql-contrib nginx curl
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
  GRANT ALL PRIVILEGES ON SCHEMA PUBLIC TO username;
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

##### Create Superuser :
```bash
python manage.py createsuperuser
```
###### Now uncomment the URLs that were previously commented.

##### Collect Static Files :

```bash
python manage.py collectstatic
```
<br/>

# Step 7: Test the website to check whether the server is running

- Run the command to run the nginx server:

  ```bash
  sudo systemctl start nginx
  ```

- You can check if Nginx is running using this command:

  ```bash
  sudo systemctl restart nginx
  ```
- Now, verify that the server is running by accessing the Auto-assigned IP in the instance dashboard.
- This should display the Nginx default page.
- Next, we need to configure Nginx to serve our website.
  

<br/>

# Step 8: Setting Domain Name

- To set a domain name for your website, you'll need to purchase a domain name from a provider such as GoDaddy or Hostinger.
- After purchasing the domain, you'll need to connect it with your instance, a process known as Domain Name Resolution.
- In your AWS Dashboard, search for "Route 53", which is a scalable DNS and domain name registration service.
- You can refer to the YouTube video to connect your EC2 instance with the domain you bought on GoDaddy:

  [https://youtu.be/hRSj2n-XKGM?si=d6T1r_PBzYydLVv9](https://youtu.be/hRSj2n-XKGM?si=d6T1r_PBzYydLVv9)

<br/>

# Step 9: Setting up gunicorn

- It is necessary to install Gunicorn and configure it to run our Python website with Nginx.
  
  ```bash
  pip install gunicorn
  ```
- It is necessary to proxy pass the server requests coming to the Nginx port to the Gunicorn port.
- We need to configure a new file to proxy pass the Gunicorn port in Nginx. To do that, let's navigate to the Nginx sites-available directory.
- Execute this command to navigate to the Nginx sites-available directory.
  ```bash
  cd /etc/nginx/sites-available/
  ```
- Run the command `ls` to check if the "default" file exists in the directory, which serves as the default configuration file for Nginx.
- The default file contains the configuration for displaying the Nginx default page. We should create another file for our website to maintain the standardization without altering the default configuration.
- Now create a new file there with your project/website name instead of `website_name`
  ```bash
  sudo nano website_name
  ```
- Executing this command will open a blank file in the nano editor.
- Please add the following configurations, making any necessary changes where needed:
  
```bash
server {
  listen 80;
  server_name your_domain_name.com www.your_domain_name.com;

  location / {
    proxy_pass http://0.0.0.0:9090;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
  }
}
```
- Now save the file using `Ctrl + O`
- Exit the file using `Ctrl + x`
- Now that we've created a new file adding the Gunicorn port to run the website, it's currently available. Next, we need to enable it.
- There is another directory called 'sites-enabled'. We need to create the same file there as well.
- Instead of creating it manually, We can simply create a link from 'sites-available' to 'sites-enabled' and synchronize it, which is called a `symbolic link`.
- From the 'sites-available' directory, execute this command to create the symbolic link, replacing 'file_name' with the name of the file you have created:
  
  ```bash
  sudo ln -s /etc/nginx/sites-available/file_name /etc/nginx/sites-enabled/file_name
  ```
- You can verify whether the symbolic link is created by navigating to the 'sites-enabled' directory using the following command:
  
  ```bash
  cd /etc/nginx/sites-enabled/
  ```
  
  ```bash
  ls
  ```
- We've added hosts for the website to run. Therefore, in our project's settings.py file, we need to include these hosts in the 'ALLOWED_HOSTS' area.
- Navigate to the directory where your settings.py file exists, and open it using the command: `sudo nano settings.py`.
- There will be a blank are for adding the "ALLOWED HOSTS", make the changes there:

  `In the 'Auto-assigned IP address' section, replace the placeholder with the IP address assigned to your instance, as displayed in your instance dashboard.`

  ```bash
  ALLOWED_HOSTS = ['Auto-assigned IP address', '0.0.0.0']
  ```
- Save the settings file by pressing `Ctrl + O`, then press `Enter` to confirm, and exit the nano editor by pressing `Ctrl + X`. After that, navigate back to your root directory.
- Now restart Nginx using this command:

  ```bash
  sudo service nginx restart
  ```
- After that, we need to run the Gunicorn server to ensure the website is being served.

  `You need to change the project_name.wsgi with the proper name which will be seen in the setting file Example : WSGI_APPLICATION = "project_name.wsgi.application" , from this only take the project_name.wsgi`

  ```bash
  gunicorn --bind 0.0.0.0:9090 project_name.wsgi
  ```
- Now, load the page using the auto-assigned IP in your browser to verify if your page is loading.

### There is a possibility that static files are not being served. If you encounter this issue, follow these steps:
- Need to give location of the static files in the ngnix configuration
