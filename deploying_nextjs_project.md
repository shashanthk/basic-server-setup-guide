## Deploying NextJS project on Linux server

#### Creating a user.

Since we are hosting a NextJS project with the pm2 service manager (similar to how we host NodeJS projects), it is better to host it under a separate user. This will help us to pull code to the server and manage the service.

    sudo adduser <user_name>

#### Installation

To host a NextJS project, it requires NodeJS with npm. Instead of installing NodeJS as a global CLI, we use a simple Node Version Manager (nvm) to handle multiple versions per user. Before installing [`nvm`](https://github.com/nvm-sh/nvm), switch to the newly created user.

    su <new_user_name>

    ## or 
    ## you can directly login to the account using SSH

Install `nvm`. You can get the latest version from the [official](https://github.com/nvm-sh/nvm?tab=readme-ov-file#installing-and-updating) Github repo.

    curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash

The above command will download the installation script and run the necessary commands for us. After that, install the NodeJS version required for your project. Let's say it requires `v20.11.0`, then run the following command:

    nvm install 20.11.0

To use the installed version:

    nvm use 20.11.0

To verify which version is in use:

    node --version

Install [`pm2`](https://pm2.keymetrics.io/) service manager:

    npm install pm2

Notice that, we are not using `-g` flag as we are going to install this for current user only. 

#### Starting the service.

Before we start the service, clone or upload the NextJS project to the newly created user's home path.

    /home/<new_user_name>/code/<nextjs_project_directory>

Once it's done, navigate to the directory.

    cd /home/<new_user_name>/code/<nextjs_project_directory>

Install dependencies:

    npm install 

    ## or

    yarn install

If you are using [`yarn`](https://yarnpkg.com/) to install dependecies for your NextJS projects, then install it like below before installing the project dependenices.

    npm install yarn

Again, we are not using `--global` flag becuase we wanted it to be local to the current user.

Start the project once to check if all the dependencies are installed or not.

    npm run start

If the above command doesn't show any errors, that's fine. We also need to ensure that the production build works before connecting it to the `pm2` service.

    npm run build

The above command will do the code analysis and confirms, the code works with production build or not.

Now, we will connect the code to the `pm2` service by issuing the below command.

    pm2 start "npm run start -- -p 3001" --name your-project-name

The `-p 3001` param will ignore the port number set in the NextJS code. Make sure the port number is free at the OS level using it. Adjust it according the requirement.

To check the status:

    pm2 status

To check the logs running (in case of any error):

    pm2 log

If everything works properly, you can check it in the browser by visiting to the URL:

    http://<server_ip>:3001

Or else you can also do a reverse proxy with your favtorite web-server (Nginx or Apache). This way you can visit to the website through a domain.

If you made any code changes and the same needs to reflected in the website, run below commands:

    npm run build

    pm2 restart <your-project-name>
