# Deploying Laravel App to DigitalOcean Droplet

## Introduction

This guide provides a step-by-step tutorial on how to deploy your Laravel application to a DigitalOcean droplet. By following this guide, you will have your Laravel app up and running on a DigitalOcean server in no time.

## Prerequisites

Before you begin, ensure you have the following:

- A DigitalOcean account
- A Laravel application ready for deployment
- SSH access to your DigitalOcean droplet
- Basic knowledge of the command line

## Step 1: Create a DigitalOcean Droplet

1. Log in to your [DigitalOcean account](https://www.digitalocean.com/).
2. Click on the "Create" button and select "Droplets".
3. Choose an image (Ubuntu 20.04 LTS is recommended).
4. Select a plan based on your requirements.
5. Add your SSH keys for secure access (cat ~/.ssh/id_rsa.pub)
6. Click "Create Droplet".

## Step 2: Connect to Your Droplet

Use SSH to connect to your droplet:

```sh
ssh root@your_droplet_ip
```

or you use vscode to connect to your droplet

1. Install the Remote - SSH extension in Visual Studio Code.
2. Click on the green button in the bottom left corner of the window.
3. Open ssh configuration file and add the following configuration:

```sh
Host digitalocean
    HostName your_droplet_ip
    User root
    IdentityFile ~/.ssh/id_rsa
```

## Step 3: Update the System

Update the package list and upgrade the system packages:

```sh
apt update && apt upgrade -y
```

## Step 4: Install Required Software 

Install the required software for your Laravel application:

```sh
apt install -y unzip curl git nginx mysql-server
```

## Step 5: Install PHP and Composer

Install PHP :

```sh
apt update
apt install -y php8.3 php8.3-cli php8.3-fpm php8.3-mbstring php8.3-xml php8.3-bcmath php8.3-curl php8.3-zip php8.3-mysql php8.3-tokenizer php8.3-intl
```

check the php version:

```sh
php -v
```


Install Composer:

```sh
curl -sS https://getcomposer.org/installer | php
mv composer.phar /usr/local/bin/composer
```

check the composer version:

```sh
composer --version
```

## Step 6: Configure MySQL

Run the MySQL security script to improve security:

```sh
mysql_secure_installation
```

Create a new MySQL database and user:

```sh
mysql -u root -p
```

```sql
CREATE DATABASE laravel_db;

CREATE USER 'laravel_user'@'localhost' IDENTIFIED BY 'strongpassword';

GRANT ALL PRIVILEGES ON laravel_db.* TO 'laravel_user'@'localhost';

FLUSH PRIVILEGES;

EXIT;
```

## Step 7: clone your laravel project

```sh
git clone https://github.com/your-repo/laravel-app.git /var/www/laravel-app
cd /var/www/laravel-app
composer install --no-dev --optimize-autoloader
cp .env.example .env
```

Edit the `.env` file with your database credentials:

```sh
nano .env
```

```sh
APP_NAME="Laravel"
APP_ENV=production
APP_KEY=
APP_DEBUG=false
APP_URL=http://your_domain.com

DB_CONNECTION=mysql
DB_HOST=localhost
DB_PORT=3306
DB_DATABASE=laravel_db
DB_USERNAME=laravel_user
DB_PASSWORD=strongpassword
```

Generate the application key:

```sh
php artisan key:generate
```

## Step 8: Set permissions:

```sh
chown -R www-data:www-data /var/www/laravel-app
chmod -R 775 /var/www/laravel-app/storage /var/www/laravel-app/bootstrap/cache
```

## Step 9: Configure Nginx

Create a new Nginx server block configuration:

```sh
nano /etc/nginx/sites-available/laravel-app or code /etc/nginx/sites-available/laravel-app
```

Add the following configuration:

```sh
server {
    listen 80;
    server_name yourdomain.com;
    root /var/www/laravel-app/public;
    index index.php index.html index.htm index.nginx-debian.html;

    location / {
        try_files $uri /index.php?$query_string;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php8.2-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.ht {
        deny all;
    }
}
```

Enable the site and restart Nginx:

```sh
ln -s /etc/nginx/sites-available/laravel /etc/nginx/sites-enabled/
nginx -t
systemctl restart nginx
```
