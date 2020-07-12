## Installing and configuring Apache2 on Ubuntu

#### Installation

1. Update the software repositories first.
	
		sudo update
		sudo upgrade

2. Install Apache2
	
		sudo apt install apache2

3. Add and allow Apache2 to firewall list. Before that check which n all apps are allowed.

		sudo ufw app list

	If you don't find Apache in list, then you can add it to the list.

		sudo ufw allow 'Apache'

	Verify the changes.
	
		sudo ufw status 

4. Apache2 related commands.

		sudo systemctl status apache2      ### shows the status
		sudo systemctl stop apache2        ### stops the server
		sudo systemctl start apache2       ### starts the server
		sudo systemctl restart apache2     ### restarts the server

5. Now, open any browser and type IP address and you'll get the default Apache page.
	
		http://your_server_ip

6. Apache2's default document root path is `/var/www/html` where we can place our website/web app codes. Here, we are going to place our code in user's home directory and point it to the Apache server.

7. Place/clone your code into user's home directory and adjust the database credentials if there any.

8. Set webiste's root directory permssion to 755. Otherwise Apache server fails accces the files and load it in browser. (We get **403 Forbidden access** error).

		sudo chmod 755 /home/user_name/project_root_folder

9. In order to point this path to Apache server, we need to edit some confirgurations files. We update Apache's virtual host files here. There's a defualt virtual host file, we copy it to make our own virtual host file.

		sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/mysite.test.conf

	The above command makes a copy of default configuration file. 

10. Now, open the file in text editor.

		sudo nano /etc/apache2/sites-available/mysite.test.conf

	And edit the following lines

		<VirtualHost *:80>
			
		     # add your domain name here
		     
		     ServerName www.example.com
		     ServerAlias www.example.com

		     ServerAdmin webmaster@localhost

		     # change document root here
		     
		     DocumentRoot /home/user_name/project_root_folder

		     ErrorLog ${APACHE_LOG_DIR}/error.log
		     CustomLog ${APACHE_LOG_DIR}/access.log combined

		     # add the following block to avoid 403 forbidden access error
 
		     <Directory /home/user_name/project_root_folder/>
		         Options Indexes FollowSymLinks
		         AllowOverride None
		         Require all granted
		     </Directory>

	    </VirtualHost>
	 

	Hit `Ctrl + O` to save the file. Use `Ctrl  + X` to exit.

11. Enable the newly added virtual host file.

		sudo a2ensite mysite.test.conf

	If you want to disable any virtual host file, then use the following command.
	
		sudo a2dissite site_name.conf

12. To test the configuration.

		sudo apache2ctl configtest

13. Restart the Apache2 to apply the changes.

