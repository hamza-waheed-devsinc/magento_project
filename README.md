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



# Magento2 MacBook Setup
Follow the following steps to setup Magento in Ubuntu

- Install Homebrew on MacBook
- Install Docker desktop on MacBook using dmg file download from official docker [website](https://www.docker.com/products/docker-desktop/)
- Install php 8.1 using homebrew ` brew install php@8.1 ` and export PATHS in the relative file eg. `.zshrc` or `.bashrc`.
- Install composer using homebrew ` brew install composer `
- Install Warden on MacBook following the [guide](https://docs.warden.dev/installing.html) and command `brew install wardenenv/warden/warden`
- Run ` warden svc up ` to pull the layers of base containers. It will automatically run those 4 containers. 
- Follow the official [guide](https://docs.warden.dev/environments/magento2.html) provided by Warden to install Magento2 step by step. Below are the steps to install a boiler plate scratch Magento2 project.
- Make a new project directory in Sites folder using the following commands
```
 mkdir -p ~/Sites/projectfolder
 cd ~/Sites/projectfolder
```
- Run ` warden env-init projectfolder magento2 `
- The result of this command is a .env file in the project root as in the below format
```  
 WARDEN_ENV_NAME=exampleproject
 WARDEN_ENV_TYPE=magento2
 WARDEN_WEB_ROOT=/

 TRAEFIK_DOMAIN=exampleproject.test
 TRAEFIK_SUBDOMAIN=app

 WARDEN_DB=1
 WARDEN_ELASTICSEARCH=0
 WARDEN_OPENSEARCH=1
 WARDEN_ELASTICHQ=0
 WARDEN_VARNISH=1
 WARDEN_RABBITMQ=1
 WARDEN_REDIS=1

 OPENSEARCH_VERSION=2.5
 MYSQL_DISTRIBUTION=mariadb
 MYSQL_DISTRIBUTION_VERSION=10.6
 NODE_VERSION=12
 COMPOSER_VERSION=2.2
 PHP_VERSION=8.1
 PHP_XDEBUG_3=1
 RABBITMQ_VERSION=3.9
 REDIS_VERSION=7.0
 VARNISH_VERSION=7.1

 WARDEN_SYNC_IGNORE=

 WARDEN_ALLURE=0
 WARDEN_SELENIUM=0
 WARDEN_SELENIUM_DEBUG=0
 WARDEN_BLACKFIRE=0
 WARDEN_SPLIT_SALES=0
 WARDEN_SPLIT_CHECKOUT=0
 WARDEN_TEST_DB=0
 WARDEN_MAGEPACK=0

 BLACKFIRE_CLIENT_ID=
 BLACKFIRE_CLIENT_TOKEN=
 BLACKFIRE_SERVER_ID=
 BLACKFIRE_SERVER_TOKEN=
 ```
- Modify the entries below in comparison
```
WARDEN_ENV_NAME=projectfolder
WARDEN_ENV_TYPE=magento2
WARDEN_WEB_ROOT=/

TRAEFIK_DOMAIN=projectfolder.test
TRAEFIK_SUBDOMAIN=app

WARDEN_DB=0
WARDEN_ELASTICSEARCH=1
WARDEN_OPENSEARCH=0
WARDEN_ELASTICHQ=0
WARDEN_VARNISH=1
WARDEN_RABBITMQ=1
WARDEN_REDIS=1

ELASTICSEARCH_VERSION=7.17
NODE_VERSION=12
COMPOSER_VERSION=2.2
PHP_VERSION=8.1
PHP_XDEBUG_3=1
RABBITMQ_VERSION=3.9
REDIS_VERSION=7.0
VARNISH_VERSION=7.1

WARDEN_SYNC_IGNORE=

WARDEN_ALLURE=0
WARDEN_SELENIUM=0
WARDEN_SELENIUM_DEBUG=0
WARDEN_BLACKFIRE=0
WARDEN_SPLIT_SALES=0
WARDEN_SPLIT_CHECKOUT=0
WARDEN_TEST_DB=0
WARDEN_MAGEPACK=0

BLACKFIRE_CLIENT_ID=
BLACKFIRE_CLIENT_TOKEN=
BLACKFIRE_SERVER_ID=
BLACKFIRE_SERVER_TOKEN=
```
- Run the following command to sign certificate ` warden sign-certificate projectfolder.test `
- Run the command `warden env up` to start fetching the layers of the project environment containers. It will automatically start the containers in the end. Wait for the initial synchronization everytime the project is started with this command. 
- Create a new file ` auth.json ` file in the root directory with json of credentials as:
``` 
{
    “http-basic”: {
        “repo.magento.com”: {
            “username”: “User_name”,
            “password”: “Password”
        }
    }
} 
```
- Create a `docker-compose.yml` file in the root directory and set the following configuration to set the DB container for the project:
```
version: "3.5"
services:

  db:
    hostname: "asiacelldir-mariadb"
    image: wardenenv/mariadb:10.4
    environment:
     - MYSQL_PASSWORD=magento
     - MYSQL_HISTFILE=/sql_history/.sql_history
     - MYSQL_ROOT_PASSWORD=magento
     - MYSQL_DATABASE=magento
     - MYSQL_USER=magento
     - PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
     - GOSU_VERSION=1.14
     - MARIADB_MAJOR=10.2
     - MARIADB_VERSION=1:10.2.44+maria~bionic
    volumes:
      - dbdata:/var/lib/mysql
      - sqlhistory:/sql_history
    ports:
      - "3306:3306"

volumes:
  dbdata:
  sqlhistory:

```
- Run ` warden shell ` to initiate a fpm shell terminal environment for Magento2 specific commands. 
- Run the below commands to create a new project 
```
 META_PACKAGE=magento/project-enterprise-edition META_VERSION=2.4.5-p1

 composer create-project --repository-url=https://repo.magento.com/ \
     "${META_PACKAGE}" /tmp/projectfolder "${META_VERSION}"

 rsync -a /tmp/projectfolder/ /var/www/html/
 rm -rf /tmp/projectfolder/
```
- Create a new terminal session in the root of project and run the command ` composer install ` to install the required dependencies. 
- Switch to fpm shell terminal and install magento2 using its command 
```
bin/magento setup:install \
     --backend-frontname=admin \
     --amqp-host=rabbitmq \
     --amqp-port=5672 \
     --amqp-user=guest \
     --amqp-password=guest \
     --db-host=db \
     --db-name=magento \
     --db-user=magento \
     --db-password=magento \
     --search-engine=elasticsearch7 \
     --elasticsearch-host=elasticsearch \
     --elasticsearch-port=9200
     --http-cache-hosts=varnish:80 \
     --session-save=redis \
     --session-save-redis-host=redis \
     --session-save-redis-port=6379 \
     --session-save-redis-db=2 \
     --session-save-redis-max-concurrency=20 \
     --cache-backend=redis \
     --cache-backend-redis-server=redis \
     --cache-backend-redis-db=0 \
     --cache-backend-redis-port=6379 \
     --page-cache=redis \
     --page-cache-redis-server=redis \
     --page-cache-redis-db=1 \
     --page-cache-redis-port=6379
```
- Run the following command to create the admin user and setting up the domain for local project environment
```
 ## Generate localadmin user
 ADMIN_PASS="admin123"
 ADMIN_USER=admin

 bin/magento admin:user:create \
     --admin-password="${ADMIN_PASS}" \
     --admin-user="${ADMIN_USER}" \
     --admin-firstname="Local" \
     --admin-lastname="Admin" \
     --admin-email="${ADMIN_USER}@example.com"
 printf "u: %s\np: %s\n" "${ADMIN_USER}" "${ADMIN_PASS}"

 ## Configure 2FA provider
 OTPAUTH_QRI=
 # Python 2: TFA_SECRET=$(python -c "import base64; print base64.b32encode('$(pwgen -A1 128)')" | sed 's/=*$//')
 # Python 3:
 TFA_SECRET=$(python3 -c "import base64; print(base64.b32encode(bytearray('$(pwgen -A1 128)', 'ascii')).decode('utf-8'))" | sed 's/=*$//')
 OTPAUTH_URL=$(printf "otpauth://totp/%s%%3Alocaladmin%%40example.com?issuer=%s&secret=%s" \
     "${TRAEFIK_SUBDOMAIN}.${TRAEFIK_DOMAIN}" "${TRAEFIK_SUBDOMAIN}.${TRAEFIK_DOMAIN}" "${TFA_SECRET}"
 )

 bin/magento config:set --lock-env twofactorauth/general/force_providers google
 bin/magento security:tfa:google:set-secret "${ADMIN_USER}" "${TFA_SECRET}"

 printf "%s\n\n" "${OTPAUTH_URL}"
 printf "2FA Authenticator Codes:\n%s\n" "$(oathtool -s 30 -w 10 --totp --base32 "${TFA_SECRET}")"

 segno "${OTPAUTH_URL}" -s 4 -o "pub/media/${ADMIN_USER}-totp-qr.png"
 printf "%s\n\n" "https://${TRAEFIK_SUBDOMAIN}.${TRAEFIK_DOMAIN}/media/${ADMIN_USER}-totp-qr.png?t=$(date +%s)"
```
- Run Magento commands below one by one sequentially in the fpm shell
```
bin/magento s:up
bin/magento s:d:c
bin/magento s:s:d -f
bin/magento c:f 
bin/magento indexer:reindex
```
- Run the local environment site using the below url's 
```
https://app.projectfolder.test/
https://app.projectfolder.test/backend/
```

#### Errors that could be experienced

##### Error 1:
- Root of project and fpm shell project environments not synced
error identification: ` Code changes not reflecting on browser frontend `
###### Solution
Terminate the fpm shell terminal and run ` warden env up ` again to create an initial synchronization again and then ` warden shell `. To test the synchronization you can create a ` test.php ` in project root and compare it if its reflected in shell environment in the terminal by running ` ls -l `

##### Error 2:
- When site is not accessible on browser and give 404. it is due to permission issues. 
###### Solution
Command to fix: 
Run below commands in the Sites folder terminal
``` 
sudo chmod -R 777 projectfolder/
sudo chmod -R 777 projectfolder/pub/
```
Run below commands in the fpm shell by getting one step back by `cd .. ` and assigning permissions to that project folder in the shell
```
sudo chmod -R 777 html/
sudo chmod -R 777 html/pub/
sudo chmod -R 777 html/pub/static
```
