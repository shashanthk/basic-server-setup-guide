## Installing PHP and configuring Laravel project on Ubuntu with Nginx

1. Install PHP and other dependent libraries

        sudo apt install php7.4 php7.4-gd php7.4-mysql php7.4-curl php7.4-intl php7.4-mbstring php7.4-bcmath php7.4-imap php7.4-xml php7.4-zip php7.4-fpm

    To check the PHP version run the following command.

        php --version

2. Install composer globally

        curl -sS https://getcomposer.org/installer | sudo php -- --install-dir=/usr/local/bin --filename=composer

    **Note:** Do not run Composer command with root user privileges. Example: `composer install`, `composer update` etc. Use a non-root user to run these commands.

3. Clone the Laravel project to your desired directory. Update the `.env` file as per the server requirements. Like database username, password, URL, and debug, etc.

4. Install dependencies using the Composer command. But don't forget to switch to a non-root user before running the `composer install` command. 

5. Update directory ownership.

        sudo chown -R www-data:www-data public
        sudo chown -R www-data:www-data storage
        sudo chown -R www-data:www-data bootstrap/cache

6. Add an Nginx configuration file to point your project to a domain.

        sudo nano /etc/nginx/sites-available/yourdomain.com

    And add the below contents to it.

        server {
            listen 80;
            server_name yourdomain.com www.yourdomain.com;
            root /path/to/laravel/project/public;

            add_header X-Frame-Options "SAMEORIGIN";
            add_header X-XSS-Protection "1; mode=block";
            add_header X-Content-Type-Options "nosniff";

            index index.html index.htm index.php;

            charset utf-8;

            location / {
                try_files $uri $uri/ /index.php?$query_string;
            }

            location = /favicon.ico { access_log off; log_not_found off; }
            location = /robots.txt  { access_log off; log_not_found off; }

            error_page 404 /index.php;

            location ~ \.php$ {
                fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
                fastcgi_index index.php;
                fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
                include fastcgi_params;
            }

            location ~ /\.(?!well-known).* {
                deny all;
            }
        }

    Save and exit.

8. Link added configuration to the sites-enabled list.

        sudo ln -s /etc/nginx/sites-available/yourdomain.com /etc/nginx/sites-enabled/

9. Test configuration

        sudo nginx -t 

    If there any syntax error, then it'll show you the error.

10. Reload Nginx 

        sudo systemctl restart nginx

    