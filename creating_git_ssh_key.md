## Creating SSH keys for GitHub and Bitbucket

1. Open the terminal and change the directory to `.ssh`.

		cd ~/.ssh

	If the directory doesn't exist, create it:

		mkdir -p ~/.ssh

    If you're on Windows, open [Git Bash](https://git-scm.com/) and type the above command to navigate to the path:

	    C:\Users\<user_name>\.ssh

    If you're on Mac or Linux, it will open the `.ssh` directory in the current user's `home` path.

2. Generate an SSH key by issuing the following command:

	    ssh-keygen -t ed25519 -f id_file_name

    Here, the `-f` parameter creates two files with the name you mentioned: one is the private key and the other is the public key.

	    id_file_name
    	id_file_name.pub

	Please change the file name according to the requirement.

3. Create a `config` file inside the `.ssh` directory and specify the Git host address, Git user, and SSH key to be used while accessing Git services:

	    touch ~/.ssh/config

4. Open the `config` file in your favorite text editor and add the following lines:

    - On Windows: `notepad ~/.ssh/config`
    - On Linux or Mac: `nano ~/.ssh/config` or `vim ~/.ssh/config`

    		# GitHub configuration

    		Host user-github
        		HostName github.com
        		User git
        		IdentityFile ~/.ssh/id_file_name_for_github

    		# Bitbucket configuration

    		Host user-bitbucket
       			HostName bitbucket.org
        		User git
	        	IdentityFile ~/.ssh/id_file_name_for_bitbucket

	Please change the `Host` value `user-github` according to the requirement. 
	
    Save and exit from the editor.

> **Note**: In some ISPs, the above configuration will not work, and you may receive an error message like "ssh: connect to host user-github port 22: Connection refused." If you face such an error, try the alternative configuration provided below:

	# GitHub configuration
	Host user-github
		HostName ssh.github.com
		User git
		Port 443
		IdentityFile ~/.ssh/id_file_name_for_github

5. Copy the content of the `id_file_name.pub` file and paste it into your GitHub account settings:

    - For GitHub: [GitHub SSH Keys](https://github.com/settings/keys)
    - For Bitbucket: [Bitbucket SSH Keys](https://bitbucket.org/account/settings/ssh-keys/)

6. Test the newly added SSH configuration:

    	ssh -T git@user-github

7. If you don't get any errors from the above command, the authentication worked. Now, you are ready to use SSH tunnel for Git operations.

8. While cloning a new repository, use the following command:

    	git clone git@user-github:some_user_name/project_repo.git

    Notice the `git@user-github:` before the repository URL.

> **Note**: If you receive an error like "Host key verification failed," run the following command to resolve it:

	ssh-keygen -f "~/.ssh/known_hosts" -R "[host_address_here]"