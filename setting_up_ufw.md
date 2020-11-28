## Setting up UFW (Uncomplicated Firewall) on Ubuntu

1. UFW is available/installed by default on most of the recent versions of Ubuntu. If you don't find it, install it by executing the command.

        sudo apt install ufw

2. Before the start and enabling the UFW service **do not forget to allow the SSH port** address. Otherwise, the service will deny the new SSH session. 

    Let's say your SSH port address is `1234` and you want to allow it through Firewall, do this.

        sudo ufw allow 1234

3. Now, enable the service.

        sudo ufw enable

4. To check the status

        sudo ufw status

6. If you want to see the list of applications that can be added to the firewall list.

        sudo ufw app list

5. Deleting rule

    Check existing rules by rule number.

        sudo ufw status numbered

    Delete rule by number

        sudo ufw delete <rule_number> 

6. If something goes wrong or if you want to disable the firewall for testing purpose while deploying the web applications,

        sudo ufw disable

7. For resetting the firewall settings

        sudo ufw reset