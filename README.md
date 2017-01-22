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
  * AWS server
  * Server IP Address: 
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
* Header files and a static library for Python (python-dev)
* Git: fast, scalable, distributed revision control system (git)
* Python Distutils Enhancements (python-setuptools)
* Python package installer (python-pip)
* Postgres: object-relational SQL database (postgresql)
* Postgres: additional facilities for PostgreSQL (postgresql-contrib)
* Python module for PostgreSQL (python-psycopg2)
* Python Virtual Environment (virtualenv)
* Python Packages:
  * Flask (Flask)
  * SQLAlchemy (sqlalchemy)

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
Remove remote login for the remote user
```
sudo nano /etc/ssh/sshd_config
```
Change the line with PermitRootLogin to
```
PermitRootLogin no
```
###4. Create Grader User
#####1. Create the user
```
sudo adduser grader
```
Create a password. Enter all the details. 

#####2. Give user sudo access
```
nano /etc/sudoers.d/grader
```
Add the following line into that file and save & exit
```
grader ALL=(ALL) ALL
```
#####3. Force the user to create a public key for login
```
sudo nano /etc/ssh/sshd_config
```
Ensure that this line exists
```
PasswordAuthentication no
```

###5.Install Required Packages
#####1. Update your Linux Server
```
sudo apt-get update
sudo apt-get upgrade
```

#####2. Install Apache HTTP Server
```
sudo apt-get install apache2
sudo apt-get libapache2-mod-wsgi
```
#####3. Install Required Python software
``` 
sudo apt-get install python-dev
sudo apt-get install python-setuptools
sudo apt-get install python-pip
sudo apt-get install git
sudo apt-get install postgresql postgresql-contrib
sudo apt-get install python-psycopg2
sudo pip install virtualenv
sudo pip install --upgrade oauth2client
```
#####4. Install your application
We use git to clone the application:
```
sudo git clone "repository-here"
```

Rename the directory to something more user friendly
```
sudo mv fullstack-item-catalog item_catalog
```
Next we rename the application to __init__.py
```
cd item_catalog
sudo mv app.py __init__.py
```

#####5. Set up your virtual environment

Inside the directory let's create a virtual environment
```
sudo virtualenv venv
```
Enter the VirtualEnvironment
```
source venv/bin/activate
```
Install the required packages
```
sudo pip install Flask
sudo pip install sqlalchemy
```
#####6. Set up your database
Create a new user for the catalog app
```
sudo adduser catalog
```
Fill in ther user's details

Log into the postgres user to update database
```
sudo su - postgres
```
Get into the PostgreSQL database
```
psql
```
Create a role for the database
```
CREATE ROLE catalog WITH LOGIN;
ALTER ROLE catalog WITH PASSWORD 'Hello123';
```
Create the database
```
CREATE DATABASE catalogdb;
```
We are all set, we can quit the database using:
```
\q
```
We can now setup our database but first we need to alter our database_setup.py
```
sudo nano /usr/share/item_catalog/database_setup.py
```
Alter the line:
```
engine = create_engine('postgresql+psycopg2://catalog:Hello123@localhost/catalogdb')
```
Save and exit and run:
```
sudo python database_setup.py
```


## Resources Used
1. The Awesome Udacity Courses
  * [Configuring Linux Web Servers](https://www.udacity.com/course/configuring-linux-web-servers--ud299)
  * [Linux Command Line Basics](https://www.udacity.com/course/linux-command-line-basics--ud595)
2. [UFW Essentials: Common Firewall Rules and Commands](https://www.digitalocean.com/community/tutorials/ufw-essentials-common-firewall-rules-and-commands)

   Used to set up the UFW firewall 
3. [Changing the SSH Port for Your Linux Server](https://www.godaddy.com/help/changing-the-ssh-port-for-your-linux-server-7306)
4. [How To Deploy a Flask Application on an Ubuntu VPS](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps)
5. [Apache HTTP Server Server Version 2.4 Getting Started](http://httpd.apache.org/docs/current/getting-started.html)
6. [mod_wsgi (Apache)](http://flask.pocoo.org/docs/0.12/deploying/mod_wsgi/)
7. [HOW DO I DISABLE SSH LOGIN FOR THE ROOT USER?](https://mediatemple.net/community/products/dv/204643810/how-do-i-disable-ssh-login-for-the-root-user)
