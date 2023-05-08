# Magento Ubuntu Setup
Follow the following steps to setup Magento in Ubuntu

- Install PHP ‘8.1’ using the [guide](https://medium.com/@laraveltuts/how-to-install-and-run-php-8-x-on-ubuntu-20-04-8f18e7565c41)
- Install Composer ‘2.2.5’
- Install MySql ‘8’
- Install Redis ‘7’
- Install Apache2
- Install Elastic Search ‘7.17’ using the [guide](https://www.elastic.co/guide/en/elasticsearch/reference/7.17/deb.html)
- Visit the official [documentation](https://experienceleague.adobe.com/docs/commerce-operations/installation-guide/system-requirements.html)
- Install all the ‘Adobe Commerce Requirements’ extensions with the command
``` sudo apt install php8.1-extension_name ```
- Download the Magento enterprise edition from the [link](https://www.dropbox.com/s/qduyuod5oii2sgb/MAGENTO_ENTERPRISE_EDITION_2_4_5_P1.zip?dl=0)
- Extract the Zip in the directory ``` /var/www/html/project-folder ```
- Grant the permission to the overall directory using command ``` sudo chmod -R 777 project-folder ```
- Change permissions in the project-folder directory by the following commands:
```
find var generated vendor pub/static pub/media app/etc -type f -exec chmod g+w {} +
find var generated vendor pub/static pub/media app/etc -type d -exec chmod g+ws {} +
chown -R :www-data .
chmod u+x bin/magento
```
- Setup a Virtual Host by creating a new file ‘mag.conf’ in the directory /etc/apache2/sites-available/ and then adding the following configuration
```
<VirtualHost *:80>
    ServerAdmin magento2@test.com
    ServerName local.magento.com
    ServerAlias www.magento.please.tn
    DocumentRoot /var/www/html/project-folder
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
    <Directory /var/www/html/project-folder>
                Options Indexes FollowSymLinks MultiViews
                AllowOverride All
                Order allow,deny
                allow from all
</Directory>
</VirtualHost>
```
- Add server name with IP in ``` /etc/hosts ```
```
127.0.0.1       local.magento.com
```
- Enable your added side by the command ``` sudo a2ensite mag.conf ```
- Restart apache2 by command ``` sudo systemctl restart apache2 ```
- Install magento by the following command in the project-folder
``` bash
bin/magento setup:install --base-url=http://local.magento.com --db-host=localhost --db-name=magento2 --db-user=user_name --db-password=password --admin-firstname=admin --admin-lastname=admin --admin-email=admin@admin.com --admin-user=admin --admin-password=admin123 --language=en_US --currency=USD --timezone=America/Chicago --use-rewrites=1 --search-engine=elasticsearch7 --elasticsearch-host=localhost --elasticsearch-port=9200
```
- Add ``` auth.json ``` file in the root directory with json of credentials as:
``` {
    “http-basic”: {
        “repo.magento.com”: {
            “username”: “User_name”,
            “password”: “Password”
        }
    }
}
```
- Upgrade the setup in project folder by the command ``` sudo bin/magento setup:upgrade ```
- If you need to use Apache2 and Nginx simultaneously then it is necessary that both are running on different ports. You can change port for either one of then

#### Errors that could be experienced

##### Error 1:
- Internal server error magento
error log trace: ``` /var/www/html/magento_project/.htaccess: Invalid command ‘RewriteEngine’, perhaps misspelled or defined by a module not included in the server configuration ```
###### Solution
Command to fix: ``` sudo a2enmod rewrite ```

##### Error 2:
- When PHP code is not interpret and being displayed as plain text
###### Solution
Command to fix: ``` apt-get install libapache2-mod-php5 ```