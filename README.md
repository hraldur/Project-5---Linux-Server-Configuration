# Project 5 - Linux Server Configuration

* Public IP: 35.176.165.249 
* SSH Port: 220
* Url: http://ec2-35-176-165-249.eu-west-2.compute.amazonaws.com/


## Server
1. Create Instance
2. Download private key
3. `mv ~/privatekey.pem ~/.ssh`
4. `chmod 600 ~/.ssh/privatekey.pem`
5. `ssh -i ~/.ssh/privatekey.pem ubuntu@35.176.165.249`


## Create user
1. `sudo adduser grader`
2. give grader sudo access:
	`sudo visudo` 
3. after `root ALL...` add:
	`grader ALL(ALL:ALL) ALL`
4. `sudo nano /etc/sudoers.d/grader`
	enter `grader ALL=(ALL) NOPASSWD:ALL`

## Update and upgrade
1. `sudo apt-get update`
2. `sudo apt-get upgrade`

## Change SSH port and configure SSH access
1. change the ssh config file
	`sudo nano /etc/ssh/sshd_config`
2. change port 22 to 2200
3. change PasswordAuthentication to yes
4. change PermitRootLogin no
5. add AllowUsers grader 
6. Generate SSH key pair locally
	`ssh-key`
7. login as grader
	`sudo su - grader`
8. `mkdir .ssh`
9. paste the public key into `sudo nano .ssh/authorized_keys`
10. `chmod 700 .ssh`
11. `chmod 644 .ssh/authorized_keys`
12. `ssh -i ~/.ssh/privatekey grader@35.176.165.249 -p 2200` 
13. `sudo nano /etc/ssh/sshd_config`
	change PasswordAuthentication to no
14. `sudo service ssh restart`

## Secure server
1. `sudo ufw default allow outgoing`
2. `sudo ufw default deny incoming`
3. `sudo ufw allow 2200/tcp`
4. `sudo ufw allow www`
5. `sudo ufw allow ntp`
6. `sudo ufw enable`

## Install and configure Apache
1. `sudo apt-get install apache2`
2. `sudo apt-get install python-setuptools libapache2-mod-wsgi`
3. `sudo service apache2 restart`
4. `sudo a2enmod wsgi`

## Install and configure PostgreSQL
1. `sudo apt-get install postgresql`
2. `sudo su - postgres`
3. `psql`
4. `CREATE DATABASE catalog;`
5. `CREATE USER catalog;`
6. `ALTER ROLE catalog WITH PASSWORD 'catalog';`
7. `GRANT ALL PRIVILIGES ON DATABASE catalog TO catalog;`
8. `\q`
9. `exit`

## Deploy the Catalog project
1. `sudo apt-get install git`
2. `git clone https://github.com/hraldur/catalog.git`
3. `sudo mv catalog /var/www`
4.	`cd catalog`
5. `sudo run_server.py __init__.py`
6. `sudo pip install virtualenv`
7. `sudo virtualenv venv`
8. `sudo chmod -R 777 venv`
9. `sudo source venv/bin/activate`
10. `sudo -H pip install flask`
11. `sudo -H pip install sqlalchemy`
12. `sudo -H pip install requests`
13. `sudo pip install --upgrade oauth2client`
14. `sudo nano catalog.wsgi`
	add 
  ```python
  #!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/catalog/")

from catalog import app as application
application.secret_key = 'secret_key'
```
15. `sudo nano /etc/apache2/sites-avalible/catalog.conf`
	add: 
  ```xml
        <VirtualHost *:80>
                ServerName 35.176.165.249
                ServerAdmin admin@35.176.165.249
                ServerAlias http://ec2-35-176-165-249.eu-west-2.compute.amazona$
                WSGIScriptAlias / /var/www/catalog/catalog.wsgi
                <Directory /var/www/catalog/catalog/>
                        Order allow,deny
                        Allow from all
                </Directory>
                Alias /static /var/www/catalog/catalog/static
                <Directory /var/www/catalog/catalog/static/>
                        Order allow,deny
                        Allow from all
                </Directory>
                ErrorLog ${APACHE_LOG_DIR}/error.log
                LogLevel warn
                CustomLog ${APACHE_LOG_DIR}/access.log combined
      </VirtualHost>
```
16. `sudo a2ensite catalog`
18. `sudo service apache2 reload`
19. open url http://ec2-35-176-165-249.eu-west-2.compute.amazonaws.com/

### Resources
https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps
http://docs.sqlalchemy.org/en/latest/core/engines.html#postgresql
