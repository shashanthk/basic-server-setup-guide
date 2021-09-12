## Compiling and installing Nginx web server from source

### Install build dependencies 

1. Update system package

        sudo apt update

2. Install build essentials (includes gcc, c++, make, etc)

        sudo apt install build-essential

3. Install [PCRE](http://pcre.org/)

    Download PCRE source code from the official [repo](https://ftp.pcre.org/pub/pcre/)

        wget https://ftp.pcre.org/pub/pcre/pcre-8.44.tar.gz
        tar -zxf pcre-8.44.tar.gz     ## extract
        cd pcre-8.44  
        ./configure     
        make
        sudo make install

4. Install [zlib](http://www.zlib.net/)

        wget http://www.zlib.net/zlib-1.2.11.tar.gz
        tar -zxf zlib-1.2.11.tar.gz
        cd zlib-1.2.11
        ./configure
        make
        sudo make install

5. Install [OpenSSL](https://www.openssl.org/)

    Download source code from the official [website](https://www.openssl.org/source/)

        wget https://www.openssl.org/source/openssl-1.1.1l.tar.gz
        tar -zxf openssl-1.1.1l.tar.gz
        cd openssl-1.1.1l

        ## please note "C" is capital
        ./Configure linux-x86_64 --prefix=/usr

        make
        sudo make install

### Download and install Nginx

1. Download latest (stable) version of Nginx source code from official [website](http://nginx.org/en/download.html)

        wget http://nginx.org/download/nginx-1.20.1.tar.gz
        tar -zxf nginx-1.20.1.tar.gz
        cd nginx-1.20.1

        ## configure with paramters
        ./configure --sbin-path=/etc/nginx/nginx --conf-path=/etc/nginx/nginx.conf --pid-path=/etc/nginx/nginx.pid --with-pcre=../pcre-8.44 --with-zlib=../zlib-1.2.11 --with-http_ssl_module --with-stream --with-mail=dynamic --http-log-path=/var/log/nginx/access_log.log --error-log-path=/var/log/nginx/error_log.log --user=www-data --group=www-data

        make 
        sudo make install

2. Start service and test if service is started

        sudo /etc/nginx/nginx           ## this path might vary depending upon how we installed

        curl -I http://127.0.0.1

        ## or

        curl -I http://localhost


3. If everything is working fine, add Nginx to `systemd` so that it can start automatically when the server is restarted.

   Create a file `/lib/systemd/system/nginx.service` and put below contents in it.

        [Unit]
        Description=The NGINX HTTP and reverse proxy server
        After=syslog.target network-online.target remote-fs.target nss-lookup.target
        Wants=network-online.target

        [Service]
        Type=forking
        PIDFile=/etc/nginx/nginx.pid
        ExecStartPre=/etc/nginx/nginx -t
        ExecStart=/etc/nginx/nginx
        ExecReload=/etc/nginx/nginx -s reload
        ExecStop=/bin/kill -s QUIT $MAINPID
        PrivateTmp=true

        [Install]
        WantedBy=multi-user.target

4. Enable and start `nginx.service` in `systemd`

        sudo systemctl enable nginx.service
        sudo start nginx.service
        sudo status nginx.service

    Note: Before enabling and starting service, make sure there's no other service is occupied port `80`. Kill any service if it's already occupied.

        fuser -k 80/tcp         ## kill port 80

### Configuring sites

1. When we install Nginx from source code, we don't get `sites-available` and `sites-enabled` directories as we get if installed through Ubuntu/Debian (`apt`) package manager. Instead, we get only one `nginx.conf` file which will be located in the `/etc/nginx/` directory. 

   We need to create and configure these directories manually.

   Create directories.

        sudo mkdir -p /etc/nginx/sites-enabled /etc/nginx/sites-available

2. Link `site-enabled` directory in `nginx.conf` file. To do that, edit `etc/nginx/nginx.conf` and add the below line in `http` section.

        include /etc/nginx/sites-enabled/*;

3. Create `default` virtual host file or if you have a domain name, create a file with the same name in `/etc/nginx/sites-available/default`. And put the below contents.

        server {

                server_name  localhost;   ## add domain here

                root   /var/www/html;
                index  index.html index.htm;
		    
                location / {
                        try_files $uri $uri/ =404; 
                }

                #error_page  404 /404.html;

                # redirect server error pages to the static page /50x.html
                error_page   500 502 503 504  /50x.html;
                location = /50x.html {
                        root   /var/www/html;
                }
        }

    Save and exit

4. Add symbolic link in `sites-enabled` directory.

        sudo ln -s /etc/sites-available/default /etc/nginx/sites-enabled/

5. Test the file

        sudo ../nginx -t

   If the above command returns no error, then file syntax is proper and we can proceed. Restart the Nginx server.

        sudo systemctl restart nginx.service

6. Check if the server is responding to the request.

        curl -I http://localhost

        ## or

        curl -I http://yourdomain.com






    

