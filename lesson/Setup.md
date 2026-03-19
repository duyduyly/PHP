# Setup

[Home](../PHP.md) > `Setup`

## Setup php with Docker
- pull php image `docker pull php:8.4-apache`
- `docker run -it --name php-env84-apache php:8.4-apache bin/bash`


## Create first project:
```text
my-project
├── index.php
├── Dockerfile
├── Docker-compose.yml
```

### index.php
```php
<?php

$host = 'localhost'; //or host.docker.internal
$username = 'root';
$password = 'password';
$dbname = 'my_database';
$port = 3307;

$conn = new mysqli_connect($host, $username, $password, $dbname, $port);

if(!$conn) {
    die("Connection failed: " . mysqli_connect_error());
}

echo "Connected successfully";
```

#
### Dockerfile
```Dockerfile
FROM php:8.4-apache
RUN docker-php-ext-install mysqli pdo pdo_mysql
COPY . /var/www/html/
```

#
### Build and run the container
```cmd
docker build -t my-php-container .
docker run -p 8080:80 --name my-php-container my-php-container
docker rm -f my-php-container (remove container)

//if have any updates
call build -> remove container -> run container
```

#
### docker-compose.yml
```yaml
version: "3.7"


services:
 web:
   build: .
   container_name: my_php_container
   ports:
     - "8000:80"
   volumes:
     - .:/var/www/html
   depends_on:
     - db
   environment:
     DB_HOST: db
     DB_PORT: 3306
     DB_DATABASE: mysql
     DB_USERNAME: root
     DB_PASSWORD: 123456


 db:
   image: mysql:8.0
   container_name: mysql_db
   restart: always
   volumes:
     - ./db_data:/var/lib/mysql
   environment:
     MYSQL_ROOT_PASSWORD: 123456
     MYSQL_DATABASE: mysql
   ports:
     - "3307:3306"


```



## Reference
- https://www.youtube.com/playlist?list=PLCakfctNSHkGYdA82WDUKF3WGyONpGiEw