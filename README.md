# fullstack-linux-config
Project 5 in Udacity Fullstack Nanodegree

## Project Details
The goal of this project is to teach us:
* How to set up a remote Linux server ensuring that it is secure
* How to deploy an application onto that server

## Project Requirements
1. Git Bash

   I used Git Bash because it served my purposes but there are other more robusts ones like [Putty](http://www.putty.org/)

## Configuration Overview

### Overall
* Ubuntu
  * upgraded 1/18/2017

### Firewall
* By default block all incoming
* By default allow all outgoing
* Allow port 2200 (SSH)
* Allow port 80 (HTTP)
* Allow port 123 (NTP)

### Users
* ken
  * sudo rights
  * public key encryption required for login
  
### Installed Software
* Apache HTTP Server (apache2)
* Python WSGI adapter module for Apache (libapache2-mod-wsgi)


## Steps Taken

###1. Connect to the Remote Server
1. Download the Private Key from my Udacity Account and save it to ~/.ssh/udacity_key.rsa
2. Sign into the server
```
ssh -i ~/.ssh/udacity_key.rsa root@xx.xx.xx.xxx
```

###2. Create a User that you will use with sudo rights
   We want to get off the root user to ensure we don't do anything stupid.
   
#####1. Create the user
```
adduser ken
```
Fill out the details

#####2. Give that user sudo privileges. We do this by adding a file with their details in the /etc/sudoers.d directory
```
nano /etc/sudoers.d/ken
```
   Inside that file we type:
```
ken ALL=(ALL) NOPASSWD:ALL
```

#####3. On your local machine, navigate to the .ssh directory and create a public key
```
ssh-keygen
```
I named it kenp5key

#####4. Copy the public key onto the server.
Copy the contents of the keyp5key.pub onto the clipboard. After logging back in, switch users to the newly created user
```
su - ken
```
Now create the .ssh directory and authorized_keys file
```
sudo mkdir .ssh
sudo touch .ssh/authorized_keys
```
Open the file and paste the contents of keyp5key.pub into it:
```
sudo nano .ssh/authorized_keys
```
You should now be able to log into the server using:
```
ssh -i ~/.ssh/kenp5key ken@xx.xx.xx.xxx
```
This is how we will login for the rest of this project

###3. Set up the Firewall
#####1. By default block all incoming
```
sudo ufw default deny incoming
```
#####2. By default, allow all outgoing
```
sduo ufw default allow outgoing
```
#####3. Open on port 2200 for SSH and allowing us to access the server
```
sudo ufw allow 2200
```
You must also change the server's SSH port to 2200
```
sudo nano /etc/ssh/sshd_config
```
Change the line 
```
Port 22
```
to 
```
Port 2200
```
Restart the ssh service
```
sudo service ssh restart
```
#####4. Open port 80 for basic HTTP
```
sudo ufw allow http
```
#####5. Open port 123 for NTP (Network Time Protocol)
```
sudo ufw allow 123
```
#####6. Turn on the firewall
```
ufw enable
```
To login in the future, you must use the line:
```
ssh -i ~/.ssh/kenp5key ken@xx.xx.xx.xx -p 2200
```

###4. Create your user

## Resources Used
1. The Awesome Udacity Courses
  * [Configuring Linux Web Servers](https://www.udacity.com/course/configuring-linux-web-servers--ud299)
  * [Linux Command Line Basics](https://www.udacity.com/course/linux-command-line-basics--ud595)
2. [UFW Essentials: Common Firewall Rules and Commands](https://www.digitalocean.com/community/tutorials/ufw-essentials-common-firewall-rules-and-commands)

   Used to set up the UFW firewall 
3. [Changing the SSH Port for Your Linux Server](https://www.godaddy.com/help/changing-the-ssh-port-for-your-linux-server-7306)
4. [How To Deploy a Flask Application on an Ubuntu VPS](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps)
5. [Apache HTTP Server Server Version 2.4 Getting Started](http://httpd.apache.org/docs/current/getting-started.html)
