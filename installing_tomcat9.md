## Installation and setup guide for Tomcat 9 on Ubuntu server:

1. Install Java 11:
   
   ```
   sudo apt update
   sudo apt install openjdk-11-jdk
   ```

   After installation, verify the version:
   
   ```
   java -version
   ```

2. Download Tomcat 9 from the official [website](https://tomcat.apache.org/download-90.cgi):

   ```
   cd /tmp
   wget https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.75/bin/apache-tomcat-9.0.75.tar.gz
   ```

3. Create a Tomcat user:

   ```
   sudo useradd -m -U -d /opt/tomcat -s /bin/false tomcat
   ```

4. Extract the downloaded Tomcat installer file and move it to the directory created above:

   ```
   tar -xf apache-tomcat-9.0.75.tar.gz
   sudo mv apache-tomcat-9.0.75 /opt/tomcat/
   ```

5. Create a symbolic link to the latest version Tomcat directory. This will allow changing the version without affecting the service file:

   ```
   sudo ln -s /opt/tomcat/apache-tomcat-9.0.75 /opt/tomcat/latest
   ```

6. Grant directory permissions to the Tomcat user:

   ```
   sudo chown -R tomcat: /opt/tomcat
   sudo sh -c 'chmod +x /opt/tomcat/latest/bin/*.sh'
   ```

7. Create a system service file to run the Tomcat service in the background:

   ```
   sudo nano /etc/systemd/system/tomcat9.service
   ```

   Add the following contents:

   ```
   [Unit]
   Description=Tomcat 9 servlet container
   After=network.target

   [Service]
   Type=forking
   User=tomcat
   Group=tomcat

   Environment="JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64"
   Environment="JAVA_OPTS=-Djava.security.egd=file:///dev/urandom -Djava.awt.headless=true"
   Environment="CATALINA_BASE=/opt/tomcat/latest"
   Environment="CATALINA_HOME=/opt/tomcat/latest"
   Environment="CATALINA_PID=/opt/tomcat/latest/temp/tomcat.pid"
   Environment="CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC"

   ExecStart=/opt/tomcat/latest/bin/startup.sh
   ExecStop=/opt/tomcat/latest/bin/shutdown.sh

   [Install]
   WantedBy=multi-user.target
   ```

   Save the file and then reload the service daemon and start the service:

   ```
   sudo systemctl daemon-reload
   sudo systemctl enable tomcat9
   sudo systemctl start tomcat9
   ```

8. If your firewall is active, allow port number `8080` to be accessed from outside:

   ```
   sudo ufw allow 8080/tcp
   ```

9. Now, you should be able to see the Tomcat default page by visiting the following URL:

   ```
   http://<server_ip>:8080/
   ```

10. Add an admin account to manage war file deployment:

    ```
    sudo nano /opt/tomcat/latest/conf/tomcat-users.xml
    ```

    Add the following contents or uncomment the block at the end of the file:

    ```
    <tomcat-users>
      <role rolename="admin-gui"/>
      <role rolename="manager-gui"/>
      <user username="admin" password="admin_123" roles="admin-gui,manager-gui"/>
    </tomcat-users>
    ```

    Additionally, modify the following files to ensure the home page is accessible anywhere after successful login:

    ```
    sudo nano /opt/tomcat/latest/webapps/manager/META-INF/context.xml
    sudo nano /opt/tomcat/latest/webapps/host-manager/META-INF/context.xml
    ```

    Comment out the following code block in both files:

    ```
    <Valve className="org.apache.catalina.valves.RemoteAddrValve"
      allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />
    ```

    Restart Tomcat to apply the changes:

    ```
    sudo systemctl restart tomcat9
    ```

11. Now, you should be able to access the Tomcat home page after a successful login.
