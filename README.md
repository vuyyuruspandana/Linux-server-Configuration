# Linux-server-Configuration
README FILE:

IP address : 54.202.142.6

Application url: http://ec2-54-202-142-6.us-west-2.compute.amazonaws.com

Summary:

1. Create a user grader - sudo adduser grader
2. Give grader sudo access - sudo nano /etc/sudoers.d/grader and add the following text
	grager ALL=(ALL) ALL
3. Update and Upgrade
	sudo apt-get update
	sudo apt-get upgrade
4. navigate to the grader - sudo su - grader
5. Change the port from 22 to 2200 
	sudo nano /etc/ssh/sshd_config
6. In the portal , Network tab add a New firewall Custom 2200/tcp
7. make new directory - sudo mkdir .ssh
8. In your local machine generate a new rsa key
	ssh-keygen
	location : /c/Users/spandana/.ssh/id_rsa/gra
9. make a new file sudo nano /.ssh/authorized_keys and copy gra.pub in that file
10. Configure the UFW
	sudo ufw default allow outgoing
	sudo ufw default deny incoming
	sudo ufw allow 2200/tcp
	sudo ufw allow www
	sudo ufw alloe ntp
	sudo ufw enable
11. Change the timezone to UTC
	sudo dpkg-reconfigure tzdata by choosing UTC
12. Change the PremitRootLogin to no in sudo nano /etc/ssh/sshd_config
13. You will now be able to login from your local host machine ssh -i ~/.ssh/gra grader@54.202.142.6 -p 2200
14. Installations:
	sudo apt-get install apache2
	sudo apt-get install libapache2-mod-wsgi-py3
15. Enable sudo a2enmod wsgi
16. sudo service apache2 start
17. sudo apt-get install git
18. Navigate cd /var/www
19. Make sudo mkdir catalog
20. sudo chown -R grader:grader catalog
21. cd catalog and git clone https://github.com/vuyyuruspandana/Item-Catalog.git catalog
22. Create file catalog.wsgi file

	import sys
	import logging
	logging.basicConfig(stream=sys.stderr)
	sys.path.insert(0, "/var/www/catalog/catalog")
	sys.path.insert(1, "/var/www/catalog")

	from catalog import app as application
	application.secret_key = 'secret_super_key'

23. navigate to cd catalog and run mv catalog_view.py __init__.py
24. Install Vitual Environment
	sudo pip install virtalenv
	sudo virtualenv venv
	source venv/bin/activate
	sudo chmod -R 777 venv
25. You are now in Virtual environment.
	sudo apt-get install python3-pip
	sudo pip3 install Flask
	sudo pip3 install httplib2 sqlalchemy oauth2client psycopg2 sqlalchemy_utils
26. sudo nano /etc/apache2/sites-available/catalog.cong

	<VirtualHost *:80>
    		ServerName mywebsite.com
    		#ServerAlias ec2-54-202-142-6.us-west-2.compute.amazonaws.com
    		ServerAdmin admin@mywebsite.com
    		#WSGIDaemonProcess catalog user=grader group=grader threads=5
    		#WSGIProcessGroup catalog
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
27. sudo a2ensite catalog
28. __init__.py file change the location of client_secrets.json to /var/www/catalog/catalog/client_secrets.json
29. PostgreSQL
	sudo apt-get install postgresql postgresql-contrib
	sudo su - postgres
	psql
	CREATE USER catalog with PASSWORD 'password';
	ALTER USER catalog CREATEDB;
	CREATE DATABASE catalog WITH OWNER catalog;
	\q
	exit
30. engine = create_engine('postgresql://catalog:password@localhost/catalog') update in __init__.py and catalog_db.py
31. sudo service apache2 restart

references:
- https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps

	

	 
