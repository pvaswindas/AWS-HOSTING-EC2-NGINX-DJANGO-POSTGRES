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
- Here we are configuring an EC2 instance with Ubuntu 64-bit OS and t3.micro or t2.micro type, which is eligible for AWS Free Tier choose that.
- Create and download your PEM file from the "Key pair" section, giving it your project name.
  `Store the PEM file in an appropriate directory and do not delete it, as it cannot be recreated.`
- Verify EC2 instance network settings and permissions: Ensure "Allow SSH," "Allow HTTPS," and "Allow HTTP" are enabled. Then, click "Launch Instance."


# Step 2: Establishing Connection with Your EC2 Instance
### Copy the SSH login command:
- On your EC2 Instance Dashboard, choose the instance you've recently created.
- Click on the "Connect" button located at the top right corner.
- Select the SSH client and copy the SSH login command from the Example section (ssh -i key_.pem ec2-user@your-ec2-instance-public-ip).

### Changing permissions to the .pem file:
- Open Command Prompt/ Git Bash (I prefer Git Bash) in the directory where the .pem file is located.
- Enter the command to change the .pem file permissions, setting it to read-only for the owner:
  
  `Make sure to replace "key_name.pem" with the actual name of your .pem file.`

  If you are using Git Bash, you can execute Linux commands. Git Bash on Windows supports Linux commands.
  
  - For Linux: "chmod 400 key_name.pem"
  - For Windows: "icacls key_name.pem /inheritance:r /grant:r %USERNAME%:R"

### Access the instance by logging in:
- Now, paste the command copied earlier from the SSH client tab.
- After pasting the command, you may encounter a prompt about host authenticity. Type "yes" and press Enter to continue connecting.

### Installing required dependencies:
```nginx
sudo apt update
```
```nginx
sudo apt install python3-pip python3-dev libpq-dev postgresql postgresql-contrib nginx curl
```

# Step 3: Setting Up Database in Postgres
- Connect to postgres   :
  ```nginx
  sudo -u postgres psql
  ```
- Create database   :

  ```nginx
  CREATE DATABASE your_database_name;
  ```
- Create user   :

  ```nginx
  CREATE USER user_name WITH PASSWORD 'password';
  ```
- Grant permissions   :
- Enter inside the created database using the command:
  
  ```nginx
  \c your_database_name
  ```
  
  ```nginx
  GRANT ALL PRIVILEGES ON DATABASE your_database_name TO user_name;
  ```
  ```nginx
  GRANT ALL PRIVILEGES ON SCHEMA PUBLIC TO username;
  ```
- Exit postgres   :

  ```nginx
  \q
  ```


# Step 4: PIP Installation & Git Cloning
### » Install pip
```nginx
sudo apt install python3-pip
```
### » Install virtual environment
```nginx
sudo apt install python3.12-venv
```
### » Create a virtual env
```nginx
python3 -m venv env
```
### » Activate virtual env
```nginx
source env/bin/activate
```
### » Install psycopg2-binary

```nginx
pip install psycopg2-binary
```
### Before cloning your repository, ensure you have a "requirements.txt" file containing the project dependencies. If you already have this file, you can proceed to clone the Git repository and continue with the next steps.
- Open your project locally using VS Code or PyCharm.
- Activate your virtual environment and navigate to the root directory of your project, where "manage.py" exists.
- Run this command:

  ```nginx
  pip freeze > requirements.txt
  ```
- Confirm the file creation by check your directory for the "requirements.txt" file.

### » Clone the git repository
##### Replace `<your-git-repository-url>` with the URL of your Git repository.

```nginx
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
```nginx
DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.postgresql_psycopg2",
        "NAME": "database_name",
        "USER": "user_name",
        "PASSWORD": "user_password",
        "HOST": "localhost",
        "PORT": "5432",
    }
}
```

### Also make changes in the static configuration
```nginx
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
```nginx
pip install -r requirements.txt 
```
###### You have to create requirements.txt in your root directory before using this command.

##### Verify the installation :
```nginx
pip freeze
```
### Migrate
##### Comment out the urls :
- Navigate into the main directory of your project, where settings.py is located, using the "cd" command.
- Open the `url.py` file in the main directory of project using the nano editor :
  
  ```nginx
  sudo nano urls.py
  ```
###### We need to comment out all the URLs except for admin.site.urls.
  ```nginx
  urlpatterns = [
    path("admin/", admin.site.urls),
    # path("", include("user.urls")),
    # path("", include("admin.urls")),
    # path("accounts/", include("allauth.urls"), name="accounts"),
  ]
  ```
###### We can revert this action after completing the migrations; otherwise, it may cause errors when executing the migration commands.

##### Run Migration commands in the same order :

```nginx
python manage.py migrate
```

##### Create Superuser :
```nginx
python manage.py createsuperuser
```
###### Now uncomment the URLs that were previously commented.

##### Collect Static Files :

```nginx
python manage.py collectstatic
```
<br/>

# Step 7: Test the website to check whether the server is running

- Run the command to run the nginx server:

  ```nginx
  sudo systemctl start nginx
  ```

- You can check if Nginx is running using this command:

  ```nginx
  sudo systemctl status nginx
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
  
  ```nginx
  pip install gunicorn
  ```
- It is necessary to proxy pass the server requests coming to the Nginx port to the Gunicorn port.
- We need to configure a new file to proxy pass the Gunicorn port in Nginx. To do that, let's navigate to the Nginx sites-available directory.
- Execute this command to navigate to the Nginx sites-available directory.
  ```nginx
  cd /etc/nginx/sites-available/
  ```
- Run the command `ls` to check if the "default" file exists in the directory, which serves as the default configuration file for Nginx.
- The default file contains the configuration for displaying the Nginx default page. We should create another file for our website to maintain the standardization without altering the default configuration.
- Now create a new file there with your project/website name instead of `website_name`
  ```nginx
  sudo nano website_name
  ```
- Executing this command will open a blank file in the nano editor.
- Please add the following configurations, making any necessary changes where needed:
  
```nginx
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
  
  ```nginx
  sudo ln -s /etc/nginx/sites-available/file_name /etc/nginx/sites-enabled/file_name
  ```
- You can verify whether the symbolic link is created by navigating to the 'sites-enabled' directory using the following command:
  
  ```nginx
  cd /etc/nginx/sites-enabled/
  ```
  
  ```nginx
  ls
  ```
- To verify the website runs, enter the default configuration file and comment out all lines. Otherwise, the default file will load when you access the IP address. Save and exit to continue.
- Navigate to the /etc/nginx/sites-enabled/

  ```nginx
  sudo nano default
  ```
- Comment out all the lines in the default configuration, save the file, and exit.
- We've added hosts for the website to run. Therefore, in our project's settings.py file, we need to include these hosts in the 'ALLOWED_HOSTS' area.
- Navigate to the directory where your settings.py file exists, and open it using the command: `sudo nano settings.py`.
- There will be a blank are for adding the "ALLOWED HOSTS", make the changes there:

  `In the 'Auto-assigned IP address' section, replace the placeholder with the IP address assigned to your instance, as displayed in your instance dashboard.`

  ```nginx
  ALLOWED_HOSTS = ['Auto-assigned IP address', '0.0.0.0']
  ```
- Save the settings file by pressing `Ctrl + O`, then press `Enter` to confirm, and exit the nano editor by pressing `Ctrl + X`. After that, navigate back to your root directory.
- Now restart Nginx using this command:

  ```nginx
  sudo service nginx restart
  ```
- After that, we need to run the Gunicorn server to ensure the website is being served.

  `You need to change the project_name.wsgi with the proper name which will be seen in the setting file Example : WSGI_APPLICATION = "project_name.wsgi.application" , from this only take the project_name.wsgi`

  ```nginx
  gunicorn --bind 0.0.0.0:9090 project_name.wsgi
  ```
- Now, load the page using the auto-assigned IP in your browser to verify if your page is loading.
---

# If you encounter an issue with static files not being served, follow these steps; otherwise, skip these and proceed to step-10:
- Need to give location of the static files in the ngnix configuration
- To do that, navigate to your root location. From there, navigate to the location where your static files are stored.
- After navigating to the directory where your static files are stored, use the command `pwd` to display the path of the working directory.
- You should copy the path provided as the output.
- Now, navigate to your website configuration file, which can be found in the 'sites-enabled' or 'sites-available' directory, and use the `sudo nano` command to open the file using the editor.
- In the server block of your configuration, there should be a location block where you've set up the proxy_pass. We also need to add another location block for serving static files inside the server block.
- You can use the following code snippet, making the change of pasting the static file location that you copied earlier using the `pwd` command:

  ```nginx
  location /static/ {
    autoindex on;
    alias /home/path/to/your/static/files/;
  }
  ```
  ` Don't forget to include the last '/' in the location of static, otherwise it will not work. `
- Save the configuration file by pressing `Ctrl + O`, then press `Enter` to confirm, and exit the nano editor by pressing `Ctrl + X`.
- Use this command:
```nginx
ps aux | grep nginx
```
The command ps aux | grep nginx is used to list all running processes (ps aux) and filter out only those lines that contain the string "nginx" (grep nginx).
- To grant permissions for accessing certain files or directories in web server configurations, we need to add the user `www-data` to the `ubuntu` group. The command to achieve this is:

```nginx
sudo usermod -a -G ubuntu www-data
```

This allows the web server, typically running under the `www-data` user, to access specific files or directories required for serving web content.

After adding the user `www-data` to the `ubuntu` group, it's common practice to navigate into the root directory of your project and use the command:

`You may need to adjust the 'static' file name in the command according to what you have named it.`

```nginx
sudo chown -R :www-data static
```

This command changes the ownership of the `static` directory and all its contents to the `www-data` group. This ensures that the web server, which typically runs under the `www-data` user or group, has the necessary permissions to read and write to the static files within your project.
- Now restart the Nginx server:
  ```nginx
  sudo service nginx restart
  ```
- Run the Gunicorn server:

  ```nginx
  gunicorn --bind 0.0.0.0:9090 project_name.wsgi
  ```
- Run the IP in browser to check whether the page is running.
---

# Step 10: Secure the Website with HTTPS

- Install Certbot and the Nginx plugin:

  ```nginx
  sudo apt install certbot python3-certbot-nginx
  ```

- Allowing HTTPS Through the Firewall

  We need to allow HTTPS traffic, so let's check if Nginx has registered it.

  Check the firewall status:
  ```nginx
  sudo ufw status
  ```

  If the status is inactive, you can skip this step. However, if it is active, run the following commands to enable HTTPS traffic. It is advisable to block all other ports except the ones you want to allow traffic
  through.

  Enable HTTPS traffic:
  
  ```nginx
  sudo ufw allow 'Nginx Full'
  
  ```
  ```nginx
  sudo ufw delete allow 'Nginx HTTP'
  ```
  
- Obtain an SSL Certificate

  To acquire an SSL certificate, execute the following command and follow the prompts:

  `Replace your_domain.com with the domain name you purchased from GoDaddy`

  ```bash
  sudo certbot --nginx -d your_domain.com -d www.your_domain.com
  ```

---

- Now a prompt will come like this:

  ```plaintext
  Obtaining a new certificate
  Performing the following challenges:
  http-01 challenge for prodproject.net
  Waiting for verification...
  Cleaning up challenges
  Deploying Certificate to VirtualHost /etc/nginx/sites-enabled/prod_project.conf
  Please choose whether or not to redirect HTTP traffic to HTTPS, removing HTTP access.
  - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
  1: No redirect - Make no further changes to the webserver configuration.
  2: Redirect - Make all requests redirect to secure HTTPS access. Choose this for
  new sites, or if you're confident your site works on HTTPS. You can undo this
  change by editing your web server's configuration.
  - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
  Select the appropriate number [1-2] then [enter] (press 'c' to cancel):
  ```
  
  You should select option **2** here to redirect all the HTTP requests to HTTPS.

---

    
- This will acquire an SSL certificate for your domain and configure Nginx to use it.

- Update Nginx Configuration

  After obtaining the SSL certificate, update your Nginx configuration file to use it. Open the configuration file in the text editor:

  ```bash
  sudo nano /etc/nginx/sites-available/your_site
  ```
  
- If the following lines are not automatically added, locate the server block in your configuration file and insert them within the server block:

  ```nginx
  listen [::]:443 ssl ipv6only=on;
  listen 443 ssl;
  ssl_certificate /etc/letsencrypt/live/your_domain.com/fullchain.pem;
  ssl_certificate_key /etc/letsencrypt/live/your_domain.com/privkey.pem;
  include /etc/letsencrypt/options-ssl-nginx.conf;
  ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;
  ```
  Save the file and exit the text editor.

- Test and Reload Nginx

  Before you restart Nginx, you should test the configuration to ensure there are no syntax errors:

  ```nginx
  sudo nginx -t
  ```
- Verifying Certbot Auto-Renewal

  Let’s Encrypt certificates expire every ninety days, prompting users to automate renewal. Certbot manages this process, but we still need to verify its functionality.

  ```nginx
  sudo certbot renew --dry-run
  ```
- Navigate to the "settings.py" file and add the domain name's to the ALLOWED_HOSTS section:

  ```nginx
  ALLOWED_HOSTS = ['Auto-assigned IP address', '0.0.0.0', 'your_domain_name.com', 'www.your_domain_name.com']
  ```
- In the settings.py file, you may also need to set DEBUG = False so that standard error pages will be shown instead of developer errors:
  
  ```nginx
  DEBUG = False
  ```

- Now you can check the page by running the server:

  ```nginx
  sudo service nginx restart
  ```

  ```nginx
  gunicorn --bind 0.0.0.0:9090 project_name.wsgi
  ```
- Verify if the page has switched from HTTP to HTTPS.

Simply close the command prompt or Git Bash; the server will continue running.
  
# The hosting process is complete!
