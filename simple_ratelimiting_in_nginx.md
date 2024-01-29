## Setting up rate limiting in Nginx

1. Edit `/etc/nginx/nginx.conf`

        http {

            ## other config

            ##
            # rate limiting setup
            ##
            include /etc/nginx/snippets/rate_limit_conf/*;
                    
            ##
            # Virtual Host Configs
            ##
                    
            # vhost files path
        }

2. Add `/etc/nginx/snippets/rate_limit_conf/connection_limit.conf`

        # The directives below limit concurrent connections from a non-whitelisted IP address to five

        limit_conn_zone         $limit zone=connlimit:10m;
        limit_conn              connlimit 5;
        limit_conn_log_level    warn;   # logging level when threshold exceeded
        limit_conn_status       429;    # the error code to return


3. Add `/etc/nginx/snippets/rate_limit_conf/request_limit.conf`

        # The code below limits the number requests from a non-whitelisted IP
        # to one every two seconds with up to 3 requests per IP delayed
        # until the average time between responses reaches the threshold.
        # Further requests over and above this limit will result
        # in an immediate error.

        limit_req_log_level  warn;
        limit_req_status     429;

        limit_req_zone       $limit zone=ip_rate_limit:10m rate=100r/m;
        limit_req            zone=ip_rate_limit burst=3 nodelay;


4. Add `/etc/nginx/snippets/rate_limit_conf/whitelist_ips.conf`

        geo $whitelist {

            # limit all IPs
            default 0;

            # the list below are not limited
            192.168.1.100/32 1;
            192.168.29.150/32 1;
        }

        map $whitelist $limit {
                0     $binary_remote_addr;
                1     "";
        }


5. Add `/etc/nginx/sites-available/somesite.com`

        server {
            
            listen 80;
            server_name somesite.com;
            root /path/to/static/content;

            location / {

                ## appply rate limiting by mentioning the same name as mentioned above `ip_rate_limit`
                limit_req zone=ip_rate_limit;

                index index.html;
            }
        }
        
6. Test the configuration before restarting the Nginx web server.

        sudo nginx -t

7. If everything is okay, restart the server

        sudo systemctl restart nginx

        ## or

        sudo service nginx restart