# Setup Basic Project Yii2 project with docker

## Download Yii2 basic project template
- https://www.yiiframework.com/download

## Create Docker files 
```dockerfile
FROM php:8.4-apache

RUN a2enmod rewrite

WORKDIR /var/www/html

RUN sed -i 's|DocumentRoot /var/www/html|DocumentRoot /var/www/html/web|g' /etc/apache2/sites-available/000-default.conf

RUN printf '\n\
<Directory /var/www/html/web>\n\
    Options Indexes FollowSymLinks\n\
    AllowOverride All\n\
    Require all granted\n\
</Directory>\n\
' >> /etc/apache2/apache2.conf
```

### Explaination:
- `RUN sed -i 's|DocumentRoot /var/www/html|DocumentRoot /var/www/html/web|g' /etc/apache2/sites-available/000-default.conf`
  - This command modifies Apache’s configuration file to change the root directory of your website.
  - `sed` → stream editor (used to modify text files)
  - `-i` → edit the file in place
  - `'s|A|B|g`' → replace A with B globally

- It replaces : `DocumentRoot /var/www/html`
- with : `DocumentRoot /var/www/html/web`

### why change is needed?
- In Yii Basic, the entry point is:
  - `web/index.php`
- So Apache must serve files from:
  - `/var/www/html/web` instead of `/var/www/html`

#
- Run 2:
```dockerfile
RUN printf '\n\
<Directory /var/www/html/web>\n\
    Options Indexes FollowSymLinks\n\
    AllowOverride All\n\
    Require all granted\n\
</Directory>\n\
' >> /etc/apache2/apache2.conf
```

- This adds a new configuration block to Apache that allows access to your Yii web directory.
- 

#
## Update Docker Compose file
```yaml
services:
  php:
    build: .
    volumes:
      - ./:/var/www/html
      - ~/.composer-docker/cache:/root/.composer/cache:delegated
      - ./:/app:delegated
    ports:
      - '8000:80'

```