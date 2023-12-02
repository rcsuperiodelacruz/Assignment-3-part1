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
1. Click *Setting* on the bottom left of the screen
2. Click *Security*
3. Click **Add SSH Key**, it will require your public key

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

5. Paste into Digital Ocean public key text box and press **Add SSH Key **

### Create a Droplet
1. Click Droplets on the left and create a droplet
2. Select the desired options and create it by pressing **Create Droplet**

## 2. Creating a New Regular User

### Accessing the Server:

Log in to your server as the root user
In your terminal enter this:
```
  ssh -i path-to-your-key root@address-of-server
```
replace *address-of-server* with the address of your Digital Ocean Server

### Creating a New User

1. Use the following command, replacing *username* with your desired username:

```
  useradd -ms username
```

3. if you want to add a password you can do the command:

```
  passwd username
```

make sure you replace "username" with the name of your user

### setting bash as the login shell

this is already done in the code above
the -m is to create a home directory for the user
the -s sets the login shell to /bin/bash

### Granting Administrative Privileges

To allow this user to perform administrative tasks, add them to the sudo group:

```
  usermod -aG sudo username
```

### SSH Access for the New User

1. first you need to copy the root users .ssh directory to the new users home directory
to do that run the following command:

```
  sudo cp -r /root/.ssh /home/username
```

make sure that you change the *username* to your new username
-r is to also copy the files within the directory

2. Then we need to change ownership of the copied directory

```
  sudo chown -R username:username /home/username.ssh
```

again, make sure to replace *username* with your new username

3. test your connection with:
```
  ssh -i path-to-your-key username@address-of-server
```


### Disabling Root SSH Access

1. to do this you need to edit the sshd_config file
run the command:

```
  sudo vim /etc/ssh/sshd_config
```

2. look for the line:

`PermitRootLogin yes`

and change it to

`PermitRootLogin no`

3. save the file and exit vim

### Restarting SSH

1. restart the ssh service with:

```
  sudo systemctl restart ssh.service
```

2. now try connecting as root with:

```
  ssh -i path-to-your-key root@address-of-server
```

you should recieve a *permission denied* error

## 4. Installing Nginx

1. to install anything you always need to update the packages
```
  sudo apt update
```
also do
```
  sudo apt upgrade
```
2. Now we can install NGINX
```
  sudo apt install nginx
```
3. Now we can start the NGINX service and check the status
to start the system type:
```
	sudo systemctl start nginx
```
to check the status type:
```
	sudo systemctl status nginx
```


## 5. Configuring Nginx to Serve a Sample Website

### Creating Website Directory
1. Create a directory for your website can call it my-site
```
  mkdir -p /var/www/my-site
```

### Adding a Sample HTML Page
1. Create an `index.html` file:
```
  sudo vim /var/www/my-site/index.html
```
This is the sample page
```
	<!DOCTYPE html>
	<html lang="en">
	<head>
	    <meta charset="UTF-8">
	    <meta name="viewport" content="width=device-width, initial-scale=1.0">
	    <title>2420</title>
	    <style>
	        body {
	            display: flex;
	            align-items: center;
	            justify-content: center;
	            height: 100vh;
	            margin: 0;
	        }
	        h1 {
	            text-align: center;
	        }
	    </style>
	</head>
	<body>
	    <h1>Hello, World</h1>
	</body>
	</html>
```

## Nginx Server Block Configuration
1. Create a new configuration file for your site
it will be in /etc/nginx/sites-available
create the file:
```
  sudo vim /etc/nginx/sites-available/my-site
```
2. Insert the following server block:
```nginx
  server {
    listen 80 default_server;
    listen [::]:80 default_server;
    
    root /var/www/my-site;
    
    index index.html index.htm index.nginx-debian.html;
    
    server_name _;
    
    location / {
      # First attempt to serve request as file, then
      # as directory, then fall back to displaying a 404.
      try_files $uri $uri/ =404;
    }
  }
```
3. Create the symbolic link to your new configuration file
```
	sudo ln -s /etc/nginx/sites-available/my-file /etc/nginx/sites-enabled/
```
4. Delete the Duplicated Link
```
  sudo unlink /etc/nginx/sites-enabled/default
```

### Testing Configuration
1. use the following line of code to test for errors
```
	sudo nginx -t
```

### Restarting Nginx
1. Restart Nginx to apply changes:
```
	sudo systemctl restart nginx
```

### View Page
1. Send an HTTP GET request
```
	curl <your-ip-address>
```
replace `your-ip-address` with your address
