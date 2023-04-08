## Creating Tomcat custom error page

1. Create below error pages

    ```
    401.jsp
    403.jsp
    404.jsp
    405.jsp
    500.jsp
    502.jsp
    ```

    Open the the file in any text editor and add the content

    ```
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>401 Unauthorized</title>
    </head>

        <body>
            <h4>
                401 Unauthorized
            </h4>
        </body>

    </html>
    ```

    Update all the files according to the HTTP response code and message.

2. Tomcat's default page is `ROOT.war`. We need to create a directory to keep above created files on the server and tell Tomcat to use these files as default error page.

    Create a directory inside `<tomcat_installation_path>/webapps/ROOT/WEB-INF` path

    ```
    cd <tomcat_installation_path>/webapps/ROOT/WEB-INF
    
    sudo mkdir jsp
    ```

    After creating the directory, place all the above files inside it and update the permission

    ```
    sudo chown -R tomcat:tomcat jsp
    ```

3. Now, we need to configure these files as default error page

    ```
    sudo nano <tomcat_installation_path>/webapps/ROOT/WEB-INF/web.xml
    ```

    Add below lines inside `<web-app>...</web-app>`

    ```
    <error-page>
        <error-code>404</error-code>
        <location>/WEB-INF/jsp/404.jsp</location>
    </error-page>
    ```

    Add all the error codes accordingly and change the file names. Save and exit from text editor.

4. Restart the Tomcat service to check the result

    ```
    sudo systemctl restart tomcat.service
    ```

5. Visit to any invalid location

    ```
    https://tomcaturl.com/abcd
    ```

    Now, Tomcat should return custom 404 error page.

6. If above config works fine, we need to do the same settings for Tomcat manager page.

    Tomcat manager UI can be found the following location

    ```
    <tomcat_installation_path>/webapps/manager
    ```

    Please note that the manager UI has `jsp` directory and error pages in it by default. Instead of altering existing files, it's recommended to keep a backup of default files.

    ```
    cd <tomcat_installation_path>/webapps/manager/WEB-INF

    sudo cp jsp jsp.bak

    sudo cp web.xml web.xml.bak
    ```

    Now, copy the custom error pages to the below location

    ```
    <tomcat_installation_path>/webapps/manager/WEB-INF/jsp
    ```

    Update file permission

    ```
    sudo chown -R tomcat:tomcat <tomcat_installation_path>/webapps/manager/WEB-INF/jsp
    ```

    Update `web.xml` file to use custom error pages and update the content inside `<web-app>...</web-app>` block.

    ```
    sudo nano <tomcat_installation_path>/webapps/manager/WEB-INF/web.xml
    ```

    Save and exit from editor.

7. Restart the Tomcat service

    ```
    sudo systemctl restart tomcat.service
    ```
    
