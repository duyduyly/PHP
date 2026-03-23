# Dockerfile Explanation (Line by Line + Deep Breakdown)

---

# Vendor Folder
- If you don't have any folder which dependency in vendor:
  - into `exec container`  -> `composer install` to install dependencies from composer.json


## 1. Base Image

```dockerfile
FROM php:8.4-apache
```

* Uses official PHP image with Apache pre-installed
* Provides a ready-to-run web server environment

### 🔍 Details

* Includes PHP + Apache integration
* Saves time (no need to install Apache manually)
* Suitable for traditional PHP apps like Yii

---

## 2. Enable Apache Rewrite

```dockerfile
RUN a2enmod rewrite
```

* Enables Apache `mod_rewrite`

### 🔍 Details

* Required for Yii routing
* Allows clean URLs (e.g. `/site/login` instead of `index.php?r=site/login`)
* Works together with `.htaccess`

---

## 3. Install System Dependencies + PHP Extensions

```dockerfile
RUN apt-get update \
 && apt-get install -y --no-install-recommends \
    libicu-dev \
    libzip-dev \
    libonig-dev \
    unzip \
    git \
 && docker-php-ext-install intl pdo pdo_mysql zip mbstring \
 && rm -rf /var/lib/apt/lists/*
```

* Installs required libraries and PHP extensions

### 🔍 Details

#### System packages:

* `libicu-dev` → required for `intl` (internationalization)
* `libzip-dev` → required for `zip`
* `libonig-dev` → required for `mbstring`
* `unzip` → Composer needs it to extract packages
* `git` → Composer may fetch packages from Git

#### PHP extensions:

* `intl` → date, locale, formatting (Yii uses it)
* `pdo` + `pdo_mysql` → database connection
* `zip` → handle compressed packages
* `mbstring` → multi-byte string support

#### Cleanup:

* `rm -rf /var/lib/apt/lists/*` → reduce image size

---

## 4. Install Composer

```dockerfile
RUN curl -sS https://getcomposer.org/installer | php \
 && mv composer.phar /usr/local/bin/composer \
 && chmod +x /usr/local/bin/composer
```

* Installs Composer globally

### 🔍 Details

* Downloads installer via `curl`
* Executes installer using PHP
* Moves binary to global path
* Makes Composer executable everywhere

---

## 5. Configure Apache DocumentRoot

```dockerfile
RUN sed -i 's|DocumentRoot /var/www/html|DocumentRoot /var/www/html/web|g' /etc/apache2/sites-available/000-default.conf
```

* Changes Apache root directory

### 🔍 Details

* Default: `/var/www/html`
* New: `/var/www/html/web`
* Required because Yii entry point is:

  ```
  web/index.php
  ```

---

## 6. Configure Directory Permissions

```dockerfile
RUN printf '\n\
<Directory /var/www/html/web>\n\
    Options Indexes FollowSymLinks\n\
    AllowOverride All\n\
    Require all granted\n\
</Directory>\n\
' >> /etc/apache2/apache2.conf
```

* Grants access and enables `.htaccess`

### 🔍 Details

#### Options:

* `Indexes` → allow directory listing
* `FollowSymLinks` → allow symlinks

#### AllowOverride:

* Enables `.htaccess`
* Needed for Yii routing

#### Require:

* Allows public access (fixes 403 errors)

---

## 7. Set Working Directory

```dockerfile
WORKDIR /var/www/html
```

* Sets default working directory

### 🔍 Details

* All next commands run inside this folder
* Matches Apache root structure

---

## 8. Copy Composer Files (Cache Optimization)

```dockerfile
COPY composer.json composer.lock ./
```

* Copies only dependency definition files

### 🔍 Details

* Enables Docker layer caching
* Prevents reinstalling dependencies on every build
* Only re-runs if dependencies change

---

## 9. Install Dependencies

```dockerfile
RUN composer install --no-interaction --prefer-dist
```

* Installs PHP dependencies

### 🔍 Details

* `--no-interaction` → non-interactive mode
* `--prefer-dist` → faster download via zip archives
* Uses composer.lock for exact versions

---

## 10. Copy Project Source Code

```dockerfile
COPY . .
```

* Copies entire project into container

### 🔍 Details

* Includes app code, config, views, etc.
* Done AFTER composer install to optimize cache

---

## 11. Set File Permissions

```dockerfile
RUN chown -R www-data:www-data /var/www/html
```

* Sets ownership to Apache user

### 🔍 Details

* `www-data` = Apache user
* Prevents permission issues
* Required for:

    * file uploads
    * cache
    * logs

---

# 🎯 Summary

This Dockerfile does:

1. Prepare PHP + Apache environment
2. Install required system libraries
3. Install Composer
4. Configure Apache for Yii
5. Optimize dependency installation (cache)
6. Copy and run the application

---

# 🧠 Key Concepts You Just Used

* Docker layering & caching
* Web server configuration (Apache)
* PHP extension compilation
* Dependency management (Composer)
* File permissions inside containers

---

# 💬 Final Insight

👉 This is already a **production-ready foundation**

If you understand this file, you’re no longer just “using Docker” —
you’re starting to **design environments** 🔥
