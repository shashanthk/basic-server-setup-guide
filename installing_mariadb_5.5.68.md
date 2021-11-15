## Installing MariaDB 5.5.68 using tarball

1. Download MariaDB ta.gz file from official website.

        wget https://archive.mariadb.org//mariadb-5.5.68/bintar-linux-systemd-x86_64/mariadb-5.5.68-linux-systemd-x86_64.tar.gz

2. Extract files and move to installation directory

        # extract files
        tar -xzvf mariadb-5.5.68-linux-systemd-x86_64.tar.gz

        # change directory name for simplicity
        mv mariadb-5.5.68-linux-systemd-x86_64 mariadb-5.5.68

        # create destination directory
        mkdir -p /var/lib/mariadb

        # move files
        mv mariadb-5.5.68 /var/lib/mariadb

3. Create a new user and group

        # add group
        groupadd mysql

        # add user and assign group
        useradd -g mysql mysql

4. Change MariaDB installation directory permission.

        chown -R mysql:mysql /var/lib/mariadb/

5. Create `/etc/my.cnf` file to tell the base directory and default user under which the MariaDB runs.

        cp /var/lib/mariadb/mariadb-5.5.68/support-files/my-small.cnf /etc/my.cnf

    Add below lines under `[mysqld]` section.

        basedir=/var/lib/mariadb/mariadb-5.5.68
        datadir=/var/lib/mariadb/mariadb-5.5.68/data
        user=mysql

6. Edit `mysql.server` and set `basedir` & `datadir` configs before adding/linking it to services

        nano /var/lib/mariadb/mariadb-5.5.68/support-files/mysql.server

    And update the paths

        basedir=/var/lib/mariadb/mariadb-5.5.68
        datadir=/var/lib/mariadb/mariadb-5.5.68/data


7. Install MariaDB

        cd /var/lib/mariadb/mariadb-5.5.68

        # install 
        ./scripts/mysql_install_db --user=mysql --basedir=/var/lib/mariadb/mariadb-5.5.68 --datadir=/var/lib/mariadb/mariadb-5.5.68/data

8. To run MariaDB in services file, link `mysql.server` file from MariaDB installation directory to `/etc/init.d`

        ln -s /var/lib/mariadb/mariadb-5.5.68/support-files/mysql.server /etc/init.d/mysql

        # update default setting
        update-rc.d mysql defaults

        # start service
        service mysql start

9. After successful installation of MariaDB, we need to set `root` password and remove test user, database from our system.

        # set root user password
        /var/lib/mariadb/mariadb-5.5.68/bin/mysqladmin -u root password 'strong-password-here'

        # remove test user and database
        /var/lib/mariadb/mariadb-5.5.68/bin/mysql_secure_installation --basedir=/var/lib/mariadb/mariadb-5.5.68

10. By default there will not be any global access for the command `mysql`. We need to add it to environment path manually.

        nano /root/.bashrc

        # add below line to the end of the file
        PATH=$PATH:/var/lib/mariadb/mariadb-5.5.68/bin

        # reload .bashrc file
        source /root/.bashrc

    >**Note**: The above command updates environment variable values only for `root` user since we are editing `/root/.bashrc` file. If you want to have access to other users as well, then add path value on respective users' `~/.bashrc` file and reload.

11. Check if MariaDB is working properly or not by issuing the below command.

        mysql -u root -p