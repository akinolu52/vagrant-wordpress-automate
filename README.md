## Steps to configure and setup vagrant provisioning to automate pushing a wordpress default website

1. Pick a ubuntu image greater than 20

    ```bash
    vagrant init spox/ubuntu-arm
    ```

2. Set the public and private network

3. Set the ram size needed for the server to 1.5GB and above

4. Add your provisioning script from the manual setup and then check the result on the IP

```bash
sudo apt update

sudo apt install apache2 ghostscript libapache2-mod-php mysql-server php php-bcmath php-curl php-imagick php-intl php-json php-mbstring php-mysql php-xml php-zip -y

sudo mkdir -p /srv/www

sudo chown www-data: /srv/www

curl https://wordpress.org/latest.tar.gz | sudo -u www-data tar zx -C /srv/www
  
cat > /etc/apache2/sites-available/wordpress.conf <<EOF
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
EOF
 
sudo a2ensite wordpress

sudo a2enmod rewrite

sudo a2dissite 000-default

sudo systemctl reload apache2 

mysql -u root -e 'CREATE DATABASE wordpress;'
mysql -u root -e 'CREATE USER wordpress@localhost IDENTIFIED BY "<your-password>";'
mysql -u root -e 'GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER ON wordpress.* TO wordpress@localhost;'
mysql -u root -e 'FLUSH PRIVILEGES;'

sudo service mysql start

sudo -u www-data cp /srv/www/wordpress/wp-config-sample.php /srv/www/wordpress/wp-config.php

sudo -u www-data sed -i 's/database_name_here/wordpress/' /srv/www/wordpress/wp-config.php
sudo -u www-data sed -i 's/username_here/wordpress/' /srv/www/wordpress/wp-config.php
sudo -u www-data sed -i 's/password_here/<your-password>/' /srv/www/wordpress/wp-config.php

systemctl restart mysql
systemctl restart apache2
```
