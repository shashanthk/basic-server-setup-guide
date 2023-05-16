## Setting up PostgreSQL 12 on Ubuntu

1. Update the package repositiory

        sudo apt update
        sudo apt upgrade

2. Install Postgres

        sudo apt install postgresql postgresql-contrib

3. While installing Postgres the installation tool will create a user `postgres` which is super user for this database. If you want to login to that account, use below command

        sudo su postgres

4. Accessing Postgres prompt/command line after switching to `postgres` account

        ## open psql shell

        psql

        ## connecting to a database

        \connect <database_name>

   Or

        sudo -u postgres psql -d <database_name>  ## this will directly connect to specified user and db

5. Create database

        CREATE DATABASE <database_name>

6. Create new user/role

        ## create user with password

        CREATE USER <user_name> WITH ENCRYPTED PASSWORD 'strongpasswordhere..'

        ## create user without password

        CREATE USER <user_name>

        ## set password later

        \password <user_name>

   **Note:** To login with `ident` based authentication, we need to create a new user which matches with exact name as in Postgres database. Refer to the official [doc](https://www.postgresql.org/docs/12/auth-methods.html)

        sudo adduser <user_name>

7. If you want to make newly created user as a super user or revert back to normal user type, run below commands

        ## to make as a super user

        ALTER USER <user_name> WITH SUPERUSER

        ## to revert back the user type as normal

        ALTER USER <user_name> WITH NOSUPERUSER

8. Grant permissions for accessing the database to newly created user.

        ## grant connection

        GRANT CONNECT ON DATABASE <database_name> TO <user_name>

        ## grant required operations to perform

        GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA <schema_name> TO <user_name>

        ## grant all privileges on all tables in the schema

        GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA <schema_name> TO <user_name>

        ## grant all privileges on all sequences in the schema

        GRANT ALL PRIVILEGES ON ALL SEQUENCES IN SCHEMA <schema_name> TO <user_name>

        ## granting all the pivileges in one shot

        GRANT ALL PRIVILEGES ON DATABASE <database_name> TO <user_name>

        ## to apply these settings to newly created/upcoming tables

        ALTER DEFAULT PRIVILEGES FOR USER <user_name> IN SCHEMA <schema_name> GRANT SELECT, INSERT, UPDATE, DELETE ON TABLES TO <user_name>

9. Taking backups

        ## backup with tables and records

        pg_dump -U <user_name> <database_name> > backupfile.sql --no-owner

        ## backup with drop table, sequence and create commands

        pg_dump -U <user_name> <database_name> > backupfile.sql --no-owner --clean

        ## backup with only structure and sequence reset

        pg_dump -U <user_name> <database_name> > backupfile.sql --no-owner --schema-only

        ## exclude one or more table data and maintain structure as it is
        
        pg_dump -U <user_name> <database_name> > backupfile.sql --no-owner --exclude-table-data=<table_name>


    Possible additional parameters

    `--table` Take one table backup with structure and data.

    `--data-only` Take only data, not structure. Can be used with `--table` parameter.

    `--exclude-table` Excludeds both table structure and table data

   `--no-privileges` or `--no-acl` Prevent dumping of access privileges (grant/revoke commands).


10. Restoring the database.

        psql -d <database_name> -f backupfile.sql -U <user_name> --set ON_ERROR_STOP=on

    Since we are taking plain text backup, Postgres will ask us to restore it with `psql` command. Do not try it with `pg_restore` command.

11. Table backup

        ## table with structure and data
        
        pg_dump -U <user_name> <database_name> --table table_name > table_backup_file.sql --no-owner

        ## only table data

        pg_dump -U <user_name> <database_name> --data-only --table table_name > table_backup_file.sql --no-owner

11. Restoring the table or table data

        psql -d <database_name> -f table_backup_file.sql -U <user_name> --set ON_ERROR_STOP=on
