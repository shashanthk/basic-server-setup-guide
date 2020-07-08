## Installing and configuring MySQL 8 on Ubuntu

#### Installation

1. Download MySQL 8 installer apt (.deb) package from [MySQL's Official Website](https://dev.mysql.com/downloads/repo/apt/)

		wget -c https://repo.mysql.com//mysql-apt-config_0.8.15-1_all.deb

2.  After downloading the file, install it by issuing the following command.
		
		sudo dpkg -i mysql-apt-config_0.8.15-1_all.deb

3. Now the installer asks for the MySQL server version (by default 8.0 will be selected), just hit OK there

4. The installer adds the latest version MySQL apt to the Ubuntu package repository. Now, we need to update the repositories in order to install the latest version of MySQL.

		sudo update
		sudo upgrade

5. It's time to install MySQL now
	
		sudo apt install mysql-server

6. The installation wizard starts the installation process of the database server. In between the installation, it'll ask for the `root` user password. Enter the new password and re-confirm the password again. 

	And also, it'll ask for the configuration of the password validator plugin. Select **Use Strong Password Encryption (Recommended)** and hit the OK button there.

7. Once we complete the installation of the MySQL server, it's time to secure the installation. Issue the following command.
	
		sudo mysql_secure_installation

	It asks the following questions.
	
	-  Remove anonymous users? (Press **y|Y** for **Yes**, any other key for No) : `y`
	-   Disallow root login remotely? (Press **y|Y** for **Yes**, any other key for No) : `y`
	-   Remove test database and access to it? (Press **y|Y** for **Yes**, any other key for No) : `y`
	-   Reload privilege tables now? (Press **y|Y** for **Yes**, any other key for No) : `y` 


8. Now, the installation is complete and you can enable/add MySQL server to system service. To do so enter the following command.

		sudo systemctl enable mysql

	To check the service status
		
		sudo systemctl status mysql

	or
	
		sudo service mysql status

#### Configuring

By default MySQL creates one user that is `root` and you can't log in to MySQL console/database without logging as `root` into the Ubuntu server. It's a security feature to restrict anyone from accessing the database from outside. And also you won't be able to log in remotely via graphical user interface tools like MySQL Workbench, SQLyog, etc. Of course, we can turn on remote login but it's highly discouraged to do so. 

We'll create a database user, grant some permissions, create one database, and assign this database to the newly created user. This way we can prevent the users from accessing other databases and performing admin (root) level jobs.

1. Login into the system's root user account.

		sudo su   ### enter the user password when prompted

2. To login to the MySQL console issue the following command.
	
		mysql -u root -p   ### hit Enter and type the MySQL root user password when prompted

3. After successfully logging into the console, create a new database.

		CREATE DATABASE `my_blog` CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

	The above command creates a new database with the name `my_blog` with the character set `utf8mb4` and collation `utf8mb4_unicode_ci`.

4. Creating new users.

		CREATE USER 'john'@'localhost' IDENTIFIED BY 'password@123';

	Above command creates a user with the name `john` and password as `password@123`

5. Granting permissions and database access to a newly created user.
	
		GRANT ALL PRIVILEGES ON *.* TO 'john'@'localhost';

	Here the `*.*` (asterisk period asterisk) denotes all databases and their tables. That means the command grants all permission and all databases access to user `john` 

	We can limit this permission to one database. For example, if we want to limit the user `john`'s access to only `my_blog` database, then we can do it like below.
	
		GRANT ALL PRIVILEGES ON my_blog.* TO 'john'@'localhost';

6. Now, we need to reload the settings in MySQL. Otherwise, the newly created user won't be able to log in and access the database(s). In order to reload the database issue the following command.
	
		FLUSH PRIVILEGES;

7. In the future somehow if you want to revoke granted permissions and database access, you can do it like below.

		REVOKE ALL PRIVILEGES ON my_blog.* FROM 'john'@'localhost';

8. To check granted permissions.

		SHOW GRANTS FOR john;

9. To drop a user 

		DROP USER 'john'@'localhost'; 

10. To login to another user 
	
		mysql -u john -p   ### hit Enter and enter the password

11. To logout
	
		quit;


#### Resetting MySQL root password

1. Stop the service

		/etc/init.d/mysql stop

2. Login to MySQL in safe-mode. We need the Ubuntu server's root password for this.

		sudo mysqld_safe --skip-grant-tables &
3. Now, it starts in safe-mode and issue the following command.
		
		mysql -u root mysql   ### hit Enter and enter the system root password when prompted

4. And change the password

		UPDATE user SET password=PASSWORD("yournewpassword") WHERE user='root';

5. Reload the changes 

		FLUSH PRIVILEGES;
