## Installing and configuring Nginx on Ubuntu

1. Update the software repositories first.
	
		sudo update
		sudo upgrade

2. Install Nginx
	
		sudo apt install nginx

3. Add and allow Nginx to the firewall list. Before that check which n apps are in the default firewall list.

		sudo ufw app list

	Add it to the list.

		sudo ufw allow 'Nginx HTTP'

    **Note:** The above command adds the only HTTP to the firewall allow list. If you want to have HTTPS as well, then use 'Nginx HTTPS'

	Verify the changes.
	
		sudo ufw status 

4. To check the installation and if firewall settings are allowing the request to the server, enter the IP address of your server in any browser. If you see the default Nginx server welcome page, then the installation and configuration are working properly.


### Secutiry related configs

1. Disable the Nginx verison being dislayed on all the error pages.

        sudo nano /etc/nginx/nginx.conf

    Uncomment the following line

        server_tokens off;