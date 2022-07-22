## Common Installation

```bash
sudo apt-get upgrade -y
```

```bash
sudo apt-get install vim git wget curl zip unzip htop software-properties-common mysql-client awscli -y
```

## Add Swap Memory

```bash
sudo fallocate -l 5G /swapfile
```
```bash
sudo chmod 600 /swapfile
```
```bash
sudo mkswap /swapfile
```
```bash
sudo swapon /swapfile
```
```bash
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

### Add Swapiness

```bash
sudo sysctl vm.swappiness=10
```
```bash
sudo vi /etc/sysctl.conf
```
```bash
vm.swappiness=10
```

## PHP - Setup

```bash
sudo add-apt-repository ppa:ondrej/php
```
```bash
sudo apt-get update -y
```
```bash
sudo apt-get install php8.1 php8.1-fpm php8.1-cli -y
```
```bash
sudo apt-get install -y php8.1-mbstring php8.1-gd php8.1-bcmath php8.1-zip php8.1-xml php8.1-intl php8.1-mysql php8.1-curl
```

```bash
sudo systemctl restart php8.1-fpm.service
```
```bash
sudo systemctl enable php8.1-fpm.service
```

## Composer - Setup

```bash
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
```
```bash
php composer-setup.php
```
```bash
php -r "unlink('composer-setup.php');"
```
```bash
sudo mv composer.phar /usr/local/bin/composer
```

## Nginx - Setup

```bash
sudo apt-get install -y nginx
```
```bash
sudo systemctl restart nginx
```
```bash
sudo systemctl enable nginx
```
```bash
sudo chown -R ubuntu:ubuntu /var/www/
```
```bash
mkdir -p /var/www/cloud-school.in/current
```
```bash
cd /var/www/cloud-school.in/current
```
```bash
composer create-project laravel/laravel .
```
```bash
sudo vi /etc/nginx/sites-available/default 
```

## Nginx - Config Code

```bash
server {
    listen 80 default_server;
    listen [::]:80 default_server;
    server_name _;
    root /var/www/cloud-school.in/current/public;

    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
    add_header X-Content-Type-Options "nosniff";

    index index.php;

    charset utf-8;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }

    error_page 404 /index.php;

    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.(?!well-known).* {
        deny all;
    }
}
```

## Restart Nginx and Permission

```bash
sudo systemctl restart nginx
```
```bash
sudo chmod -R 777 /var/www/cloud-school.in/current/storage
```