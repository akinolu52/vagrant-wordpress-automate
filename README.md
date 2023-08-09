## Steps to configure and setup vagrant provisioning to automate pushing a wordpress default website

1. Pick a ubuntu image greater than 20

    ```bash
    vagrant init spox/ubuntu-arm
    ```

2. Set the public and private network

3. Set the ram size needed for the server to 1.5GB and above

4. Install the image and login to vagrant

   ``` bash
    vagrant up

    vagrant ssh
   ```

5. Switch to root user

   ```bash
   sudo -i
   ```

    (optional) - you can choose to change hostname (type 'i' for insert mode and then exit with :wq), then logout, login back and switch to root user.

    ```bash
    vim /etc/hostname {finance}

    hostname {finance}

    exit

    exit

    vagrant ssh

    sudo -i
    ```

6. Follow the steps [here](https://ubuntu.com/tutorials/install-and-configure-wordpress#1-overview) 

    ```bash
    sudo apt update

    sudo apt install apache2 ghostscript libapache2-mod-php mysql-server php php-bcmath php-curl php-imagick php-intl php-json php-mbstring php-mysql php-xml php-zip -y

    sudo mkdir -p /srv/www

    sudo chown www-data: /srv/www

   curl https://wordpress.org/latest.tar.gz | sudo -u www-data tar zx -C /srv/www

   vim /etc/apache2/sites-available/wordpress.conf

   <VirtualHost *:80>
        DocumentRoot /srv/www/wordpress
        <Directory /srv/www/wordpress>
            Options FollowSymLinks
            AllowOverride Limit Options FileInfo
            DirectoryIndex index.php
            Require all granted
        </Directory>
        <Directory /srv/www/wordpress/wp-content>
            Options FollowSymLinks
            Require all granted
        </Directory>
    </VirtualHost>

    sudo a2ensite wordpress

    sudo a2enmod rewrite

    sudo a2dissite 000-default

    sudo systemctl reload apache2 

    sudo mysql -u root

    CREATE DATABASE wordpress;

    CREATE USER wordpress@localhost IDENTIFIED BY '<your-password>';

    GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER ON wordpress.* TO wordpress@localhost;

    FLUSH PRIVILEGES;

    quit;

    sudo service mysql start

    sudo -u www-data cp /srv/www/wordpress/wp-config-sample.php /srv/www/wordpress/wp-config.php

    sudo -u www-data sed -i 's/database_name_here/wordpress/' /srv/www/wordpress/wp-config.php
    sudo -u www-data sed -i 's/username_here/wordpress/' /srv/www/wordpress/wp-config.php
    sudo -u www-data sed -i 's/password_here/<your-password>/' /srv/www/wordpress/wp-config.php

    sudo -u www-data vim /srv/www/wordpress/wp-config.php

    {change the defined constants}

    ```

    explanation:

    * update all apt dependencies
    * install wordpress dependencies
    * make a directory where wordpress will be installed
    * set necessary ownership of the directory to `www-data`
    * download the latest version of wordpress into the the directory
    * create a configuration file for wordpress
    * configuration file
    * enable the wordpress site
    * enable url rewrite
    * disable default site
    * reload apache2 config service
    * sign into mysql as root user
    * create a database called wordpress
    * create user for the database and set password for it
    * grant privileges to the created user
    * reload
    * exit
    * start & enable mysql service
    * copy wordpress config files
    * set database name, username and password
    * open the config file and change '_KEY' constants with these [data](https://api.wordpress.org/secret-key/1.1/salt/)

7. check your website, you can use the ip

    ```bash
    ip addr show
    ```

8. Check out the site set up your wordpress admin

9. Leave root then vagrant user

    ```bash
    exit
    
    exit
    ```

10. Power down vagrant to end

    ```bash
    vagrant halt
    ```
