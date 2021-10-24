## Copying SSH Key from local to remote server

1. Open terminal and change directory to `~/.ssh`

2. Generate SSH key by issuing the following command.

	    ssh-keygen -t ed25519 -C "yourgit@email.com" -f id_file_name

3. Copy generated public key to server.

        ssh-copy-id -i <id_file_name> user@remote-host 


    Or

        cat ~/.ssh/<id_file_name>.pub | ssh user@remote-host 'cat >> ~/.ssh/authorized_keys'

    >Note: If you are using custom SSH port, mention it with `-p <port_number>` along with above command.

4. Verify if SSH key successfully copied or not.

        ssh user@remote-host 

    This time it should login to remote system without password