# DOCKER PHP-FPM NGINX MYSQL 
Simple docker-compose based configurations with the base images of `NGINX`, `MYSQL`, and a custom image of `PHP-FPM`.
Ideal for the development environment, can be used in production with small changes eg. for WordPress.

## Versions of Base Images
 * `nginx:1.17.0`
 * `mysql:5.7`
 * `php:7.3-fpm` customised see [dockerfile] for more details

## Installation or Download
Run
```
git clone https://github.com/syedusmanali/docker-phpfpm-nginx-mysql.git
```

## Configurations
* At image build time, PHP image can be modified as required eg installation of PHP extensions which can be specified in `php-fpm/dockerfile`.
See [docker-php-extension-installer] for more details on PHP extensions. 
* At image build time, for PHP custom configurations can be provided in [custom_php.ini] or as a separate file inside the [customPHPConf] folder.
* NGINX configurations can be configured in [mysite.conf] or you can create your own NGINX configuration file inside the [nginx] folder.
* Rename the [.env.example] to `.env` and set the values as required or default env values will be used
    * HOST_CODEBASE_DIR=`/var/www/html/`
    * MYSQL_ROOT_PASSWORD=`rootpassword`
    * MYSQL_DATABASE=`mydatabase`
    * MYSQL_USER=`siteusername`
    * MYSQL_PASSWORD=`sitepassword`
 
## Running the containers
Inside the terminal window go to  `docker-phpfpm-nginx-mysql` base folder by running

```
cd [basepath]/docker-phpfpm-nginx-mysql
```
Next to bring the containers up, Run 
```
docker-compose up
```

##Default Access
* Your PHP application should be accessible at `http://localhost` or `http://127.0.0.1`.
* Inside the containers, MYSQL database should be accessible with the hostname `mysql` and port `3306`.
* Outside the containers, MYSQL database should be accessible with the hostname `localhost` and port `3306`.

[docker-php-extension-installer]: https://github.com/mlocati/docker-php-extension-installer
[.env.example]: .env.example
[mysite.conf]: nginx/mysite.conf
[custom_php.ini]: php-fpm/customPHPConf/custom_php.ini
[dockerfile]: php-fpm/dockerfile
