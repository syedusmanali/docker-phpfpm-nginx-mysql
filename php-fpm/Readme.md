# DOCKER CUSTOM PHP IMAGE
Custom `PHP` image with basic `PHP` extensions and custom `PHP` configurations `php.ini`

## Versions of Base Images
 * `php:7.3-fpm` customised see `dockerfile` for more details

## Installation or Download
Copy the current `php-fpm`folder into your machine as it is

## Configurations
* PHP image can be modified as required eg installation of PHP extensions which can be specified in [dockerfile]. 
See [docker-php-extension-installer] for more details on PHP extensions.
* PHP custom configurations can be provided in [custom_php.ini] or as a separate file inside the [customPHPConf] folder.
 
## Building the image
Inside the terminal window go to the base folder of dockerfile by running

```
cd [basepath]/php-fpm
```
And to create the image Run 
```
docker build -t syedusmanali/php-fpm .
``` 


## Running Container
We will use PHP-FPM with NGINX to serve our PHP app. Doing so will allow us to setup more complex configuration, serve static assets using NGINX.

### Step 1: Create a network

```console
$ docker network create mycustomnetwork
```

or using Docker Compose:

```yaml
version: '3'
networks:
  mycustomnetwork: ~
```

### Step 2: Create a server block

Let's create an NGINX server block to reverse proxy to our PHP-FPM container.

```nginx
server {
      listen 80 default_server;
      server_name localhost;
      root /var/www/codebase;
      index index.php;
      charset utf-8;
      
      location / {
          try_files $uri $uri/ /index.php$is_args$query_string;
      }
  
      # Specify what happens when PHP files are requested
      location ~ \.php$ {
          fastcgi_split_path_info ^(.+\.php)(/.+)$;
          #fastcgi_pass [php_fpm_container_name]:9000
          fastcgi_pass phpfpm:9000;
          fastcgi_index index.php;
          include fastcgi_params;
          fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
          fastcgi_intercept_errors off;
          fastcgi_buffer_size 16k;
          fastcgi_buffers 4 16k;
          fastcgi_read_timeout 300;
          proxy_read_timeout 360;
      }
}
```

Copy the server block above, saving the file somewhere on your host. We will mount it as a volume in our NGINX container.

### Step 3: Run the PHP-FPM image with a specific name

Docker's linking system uses container ids or names to reference containers. We can explicitly specify a name for our PHP-FPM server to make it easier to connect to other containers.

```console
$ cd [basepath]/php-fpm
```
Note : `-d` flag is to make the container run in background.
```console
$ docker run -d --name phpfpm --network mycustomnetwork -v /path/to/mysite/:/var/www/codebase syedusmanali/php-fpm
```

or using Docker Compose:

```yaml
services:
  phpfpm:
    image: 'syedusmanali/php-fpm:latest'
    networks:
      - mycustomnetwork
    volumes:
      - /path/to/app:/var/www/codebase
```

### Step 4: Run the NGINX image

```console
$ docker run -it -v /path/to/nginxconf/:/etc/nginx/conf.d/ --network mycustomnetwork -p 80:80 nginx:1.17.0
```

or using Docker Compose:

```yaml
services:
  nginx:
    image: 'nginx:1.17.0'
    depends_on:
      - phpfpm
    networks:
      - mycustomnetwork
    ports:
      - '80:80'
    volumes:
      - /path/to/ngnixcong/:/etc/nginx/conf.d/
```
### Access
Now the application should be accessible at `http://localhost`

## Entering the PHP container

```console
$ docker exec -it phpfpm bash
```
[docker-php-extension-installer]: https://github.com/mlocati/docker-php-extension-installer
[custom_php.ini]: customPHPConf/custom_php.ini
[dockerfile]:dockerfile
[customPHPConf]: customPHPConf