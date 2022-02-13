## Installing NodeJS on Ubuntu

1. Change directory to temporary.

        cd /tmp

2. Download NodeJS install script using cURL

        curl -sL https://deb.nodesource.com/setup_14.x -o nodesetup.sh

    You can change the version of NodeJS according to your requirement. Just change the first part and let `x` as it is. For example `15.x`

3. Make `nodesetu.sh` as an executable script.

        sudo bash nodesetup.sh

4. Install NodeJS

        sudo apt install nodejs
        sudo apt install build-essential

6. Check NodeJS version

        node --version

5. Check NPM version

        npm --version
