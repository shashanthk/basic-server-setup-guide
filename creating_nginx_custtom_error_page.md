## Creating Nginx custom error page

1. Create an HTML file 

    ```
    sudo nano /usr/share/nginx/html/error.html
    ```

    And add below contents

    ```html
    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="utf-8">
        <title><!--# echo var="status" default="" --> <!--# echo var="status_text" default="Something went wrong" --></title>
        <meta name="viewport" content="width=device-width, initial-scale=1">
    </head>
        <body>
            <h4><!--# echo var="status" default="" --> <!--# echo var="status_text" default="Something went wrong" --></h4>
        </body>
    </html>
    ```

    Update the file ownership.

    ```
    sudo chown -R www-data:www-data /usr/share/nginx/
    ```

2. List HTTP status codes and messages to be handled in Nginx sever. Create a new file and add the contents.

    ```
    sudo nano /etc/nginx/snippets/error_page.conf
    ```

    Add below contents

    ```ini
    error_page 400 401 402 403 404 405 406 407 408 409 410 411 412 413 414
                415 416 417 418 421 422 423 424 425 426 428 429 431 451
                500 501 502 503 504 505 506 507 508 510 511 /error.html;

    map $status $status_text {
        400 'Bad Request';
        401 'Unauthorized';
        402 'Payment Required';
        403 'Forbidden';
        404 'Not Found';
        405 'Method Not Allowed';
        406 'Not Acceptable';
        407 'Proxy Authentication Required';
        408 'Request Timeout';
        409 'Conflict';
        410 'Gone';
        411 'Length Required';
        412 'Precondition Failed';
        413 'Payload Too Large';
        414 'URI Too Long';
        415 'Unsupported Media Type';
        416 'Range Not Satisfiable';
        417 'Expectation Failed';
        418 'I\'m a teapot';
        421 'Misdirected Request';
        422 'Unprocessable Entity';
        423 'Locked';
        424 'Failed Dependency';
        425 'Too Early';
        426 'Upgrade Required';
        428 'Precondition Required';
        429 'Too Many Requests';
        431 'Request Header Fields Too Large';
        451 'Unavailable For Legal Reasons';
        500 'Internal Server Error';
        501 'Not Implemented';
        502 'Bad Gateway';
        503 'Service Unavailable';
        504 'Gateway Timeout';
        505 'HTTP Version Not Supported';
        506 'Variant Also Negotiates';
        507 'Insufficient Storage';
        508 'Loop Detected';
        510 'Not Extended';
        511 'Network Authentication Required';
        default 'Something is wrong';
    }
    ```

3. Create a location block which will handle error codes.

    ```
    sudo nano /etc/nginx/snippets/error_handler.conf
    ```

    Add content

    ```ini
    location = /error.html {
        ssi on;
        internal;
        auth_basic off;
        root /usr/share/nginx/html;
    }
    ```

4. Now, we can include the above handler config file in all our Nginx vhost files.

    ```
    sudo nano /etc/nginx/sites-available/abc.com
    ```

    ```ini
    server {

        listen 80;
        root /var/www/html;

        index index.html;

        server_name abc.com;

        location / {
                try_files $uri $uri/ =404;
        }

        ##
        # other location blocks if there are any
        ##

        ## include the global error handler config add below line in every vhost file

        include snippets/error_handler.conf;

    }
    ```

5. Test the configuration 

    ```
    sudo nginx -t
    ```

    If everything is okay, restart the Nginx sever

    ```
    sudo systemctl restart nginx
    ```

References:

- https://github.com/denysvitali/nginx-error-pages

- https://blog.adriaan.io/one-nginx-error-page-to-rule-them-all.html

- https://www.tecmint.com/create-custom-nginx-error-page/
