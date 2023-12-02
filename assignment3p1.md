# Debian 12 Server Setup Tutorial on DigitalOcean

## Due date: Friday December 01

This tutorial guides you through setting up a Debian 12 server on DigitalOcean. You will create a new user with administrative privileges, configure SSH access, disable root SSH access, install Nginx, and set up a sample website.

### Objectives

1. Create a new regular user who can:
   - Perform administrative tasks.
   - Use bash as their login shell.
   - Access the server via SSH.
2. Prevent the root user from connecting to the server via SSH.
3. Install Nginx.
4. Configure Nginx to serve a sample website.

## 1. Creating a New Regular User

**Accessing the Server:**

Log in to your server as the root user:

> ssh -i path-to-your-key root@address-of-server


**Creating a New User:**

Use the following command, replacing `username` with your desired username:

> useradd -ms username

if you want to add a password you can do the command:

> passwd username

make sure you replace "username" with the name of your user

**setting bash as the login shell**

this is already done in the code above
the -m is to create a home directory for the user
the -s sets the login shell to /bin/bash

**Granting Administrative Privileges:**

To allow this user to perform administrative tasks, add them to the sudo group:

> usermod -aG sudo username

**SSH Access for the New User:**

first you need to copy the root users .ssh directory to the new users home directory
to do that run the following command:

> sudo cp -r /root/.ssh /home/username

make sure that you change the username to your new username
-r is to also copy the files within the directory

Then we need to change ownership of the copied directory

> sudo chown -R username:username /home/username.ssh

again, make sure to replace "username" with your new username

test your connection with
> ssh -i path-to-your-key username@address-of-server


## 2. Disabling Root SSH Access

to do this you need to edit the sshd_config file
run the command:

> sudo vim /etc/ssh/sshd_config

look for the line:

> PermitRootLogin yes

and change it to

> PermitRootLogin no

save the file and exit vim

**Restarting SSH**

restart the ssh service with:

> sudo systemctl restart ssh.service

now try connecting as root with:

> ssh -i path-to-your-key root@address-of-server

you should recieve a "permission denied" error

## 3. Installing Nginx

to install anything you always need to

**Update Package Lists:**


## Configuring Nginx to Serve a Sample Website

1. **Creating Website Directory:**
   - Create a directory for your website:
     ```bash
     mkdir -p /var/www/mywebsite/html
     ```
   - Assign ownership:
     ```bash
     sudo chown -R $USER:$USER /var/www/mywebsite/html
     ```

2. **Adding a Sample HTML Page:**
   - Create an `index.html` file:
     ```bash
     nano /var/www/mywebsite/html/index.html
     ```
   - Add sample HTML content:
     ```html
     <!DOCTYPE html>
     <html>
     <head>
         <title>Welcome to My Website</title>
     </head>
     <body>
         <h1>Hello, World!</h1>
         <p>This is a sample website on my Debian server.</p>
     </body>
     </html>
     ```

3. **Nginx Server Block Configuration:**
   - Create a configuration file for your site:
     ```
     sudo nano /etc/nginx/sites-available/mywebsite
     ```
   - Insert the following server block:
     ```nginx
     server {
         listen 80;
         server_name mywebsite.com www.mywebsite.com;

         root /var/www/mywebsite/html;
         index index.html;

         location / {
             try_files $uri $uri/ =404;
         }
     }
     ```
   - Enable this configuration:
     ```bash
     sudo ln -s /etc/nginx/sites-available/mywebsite /etc/nginx/sites-enabled/
     ```

4. **Testing Configuration:**
   - Test for errors:
     ```bash
     sudo nginx -t
     ```

5. **Restarting Nginx:**
   - Restart Nginx to apply changes:
     ```bash
     sudo systemctl restart nginx
     ```