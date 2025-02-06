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
5. Add your SSH keys for secure access.
6. Click "Create Droplet".

## Step 2: Connect to Your Droplet

Use SSH to connect to your droplet:

```sh
ssh root@your_droplet_ip
```

## Step 3: Update the System

Update the package list and upgrade the system packages:

```sh
apt update && apt upgrade -y
```

## Step 4: Install Required Packages

Install the required packages for Laravel:

```sh
apt install -y unzip curl git nginx mysql-server
```

## Step 5: Install PHP and Composer

Install PHP :

```sh
apt update
apt install -y php8.3 php8.3-cli php8.3-fpm php8.3-mbstring php8.3-xml php8.3-bcmath php8.3-curl php8.3-zip php8.3-mysql php8.3-tokenizer php8.3-intl
```

Install Composer:

```sh
curl -sS https://getcomposer.org/installer | php
mv composer.phar /usr/local/bin/composer
```


