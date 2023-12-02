# LinuxAssignment3

### Objectives

1. Starting from a Fresh Debian 12 server on digitalocean
2. Create a new regular user who can:
   - Perform administrative tasks.
   - Use bash as their login shell.
   - Access the server via SSH.
3. Prevent the root user from connecting to the server via SSH.
4. Install Nginx.
5. Configure Nginx to serve a sample website.

## 1. Starting Debian 12 server on Digital Ocean

### Making ssh key-pair

1. you need to create a new .ssh directory
run the following code in your terminal:
```
	mkdir .ssh
```

2. Now you need to create the ssh key pair
run the following code in your terminal:
```
	ssh-keygen -t ed25519 -f .ssh/do-key -C "your-email-address"
```

### Make a digital ocean account

You need to add your personal information

### Add ssh key to digital ocean account
1. Click *setting* on the bottom left of the screen
2. Click *security*
3. Click *Add SSH Key* and it will require your public key

4. To retrieve your public key enter this command in your terminal:
For Windows
```
	Get-Content C:\Users\user-name\.ssh\do-key.pub | Set-Clipboard
```
For MacOS
```
	pbcopy < ~/.ssh/your-key.pub
```

remember to replace *user-name* with your preferred user name

5. Paste into Digital Ocean public key text box and press ***Add SSH Key ***

### Create a Droplet
1. Click Droplets on the left and create a droplet
2. Select the desired options and create it by pressing **Create Droplet**

## 2. Creating a New Regular User

### Accessing the Server:

Log in to your server as the root user
In your terminal enter this:
```ssh -i path-to-your-key root@address-of-server```


### Creating a New User

1. Use the following command, replacing username with your desired username:

```useradd -ms username```

3. if you want to add a password you can do the command:

```passwd username```

make sure you replace "username" with the name of your user

### setting bash as the login shell

this is already done in the code above
the -m is to create a home directory for the user
the -s sets the login shell to /bin/bash

### Granting Administrative Privileges

To allow this user to perform administrative tasks, add them to the sudo group:

```usermod -aG sudo username```

### SSH Access for the New User

1. first you need to copy the root users .ssh directory to the new users home directory
to do that run the following command:

```sudo cp -r /root/.ssh /home/username```

make sure that you change the username to your new username
-r is to also copy the files within the directory

2. Then we need to change ownership of the copied directory

```sudo chown -R username:username /home/username.ssh```

again, make sure to replace "username" with your new username

3. test your connection with:
```ssh -i path-to-your-key username@address-of-server```


### Disabling Root SSH Access

1. to do this you need to edit the sshd_config file
run the command:

```sudo vim /etc/ssh/sshd_config```

2. look for the line:

`PermitRootLogin yes`

and change it to

`PermitRootLogin no`

3. save the file and exit vim

### Restarting SSH

1. restart the ssh service with:

```sudo systemctl restart ssh.service```

2. now try connecting as root with:

```ssh -i path-to-your-key root@address-of-server```

you should recieve a "permission denied" error

## 4. Installing Nginx

to install anything you always need to

**Update Package Lists:**


## 5. Configuring Nginx to Serve a Sample Website

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
