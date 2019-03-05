//Docker Project Setup with laravel
//Setup and configure docker first


//docker compose install and configure
//Step -->1
sudo curl -L "https://github.com/docker/compose/releases/download/1.23.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

//Step -->2
sudo chmod +x /usr/local/bin/docker-compose




1. composer create-project --prefer-dist laravel/laravel laravel-app "5.7.*"

2. cd laravel-app

//Next, use Docker's composer image to mount the directories that you will need for your Laravel project and avoid the overhead of installing Composer globally:
 
3. docker run --rm -v $(pwd):/app composer install

4 cd ..

// set permissions on the project directory so that it is owned by your non-root user:

5. sudo chown -R $USER:$USER laravel-app

```javascript

6.nano laravel-app/docker-compose.yml

7.//put the following content for yml file

	version: '3'
	services:

	  #PHP Service
	  app:
	    build:
	      context: .
	      dockerfile: Dockerfile
	    image: digitalocean.com/php
	    container_name: app
	    restart: unless-stopped
	    tty: true
	    environment:
	      SERVICE_NAME: app
	      SERVICE_TAGS: dev
	    working_dir: /var/www
	    volumes:
	      - ./:/var/www
	      - ./php/local.ini:/usr/local/etc/php/conf.d/local.ini
	    networks:
	      - app-network

	  #Nginx Service
	  webserver:
	    image: nginx:alpine
	    container_name: webserver
	    restart: unless-stopped
	    tty: true
	    ports:
	      - "8080:80"
	      - "443:443"
	    volumes:
	      - ./:/var/www
	      - ./nginx/conf.d/:/etc/nginx/conf.d/
	    networks:
	      - app-network

	  #MySQL Service
	  db:
	    image: mysql:5.7.22
	    container_name: db
	    restart: unless-stopped
	    tty: true
	    ports:
	      - "3307:3306"
	    environment:
	      MYSQL_DATABASE: laravel
	      MYSQL_ROOT_PASSWORD: xxx423401
	      SERVICE_TAGS: dev
	      SERVICE_NAME: mysql
	    volumes:
	      - dbdata:/var/lib/mysql/
	      - ./mysql/my.cnf:/etc/mysql/my.cnf
	    networks:
	      - app-network

	#Docker Networks
	networks:
	  app-network:
	    driver: bridge
	#Volumes
	volumes:
	  dbdata:
	    driver: local

```

```javascript
8. nano laravel-app/Dockerfile

9. put the following chnages in the dockrerfile


	FROM php:7.2-fpm

	# Copy composer.lock and composer.json
	COPY composer.lock composer.json /var/www/

	# Set working directory
	WORKDIR /var/www

	# Install dependencies
	RUN apt-get update && apt-get install -y \
	    build-essential \
	    mysql-client \
	    libpng-dev \
	    libjpeg62-turbo-dev \
	    libfreetype6-dev \
	    locales \
	    zip \
	    jpegoptim optipng pngquant gifsicle \
	    vim \
	    unzip \
	    git \
	    curl

	# Clear cache
	RUN apt-get clean && rm -rf /var/lib/apt/lists/*

	# Install extensions
	RUN docker-php-ext-install pdo_mysql mbstring zip exif pcntl
	RUN docker-php-ext-configure gd --with-gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ --with-png-dir=/usr/include/
	RUN docker-php-ext-install gd

	# Install composer
	RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

	# Add user for laravel application
	RUN groupadd -g 1000 www
	RUN useradd -u 1000 -ms /bin/bash -g www www

	# Copy existing application directory contents
	COPY . /var/www

	# Copy existing application directory permissions
	COPY --chown=www:www . /var/www

	# Change current user to www
	USER www

	# Expose port 9000 and start php-fpm server
	EXPOSE 9000
	CMD ["php-fpm"]

```

10. mkdir laravel-app/php

```javascript

11. nano laravel-app/php/local.ini

12. put the following ini changes in the .ini file

	upload_max_filesize=40M
	post_max_size=40M

```

13. mkdir -p laravel-app/nginx/conf.d


```javascript
14. nano laravel-app/nginx/conf.d/app.conf

15.put nginx chnages in the .conf file


	server {
	    listen 80;
	    index index.php index.html;
	    error_log  /var/log/nginx/error.log;
	    access_log /var/log/nginx/access.log;
	    root /var/www/public;
	    location ~ \.php$ {
		try_files $uri =404;
		fastcgi_split_path_info ^(.+\.php)(/.+)$;
		fastcgi_pass app:9000;
		fastcgi_index index.php;
		include fastcgi_params;
		fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
		fastcgi_param PATH_INFO $fastcgi_path_info;
	    }
	    location / {
		try_files $uri $uri/ /index.php?$query_string;
		gzip_static on;
	    }
	}
```

16. mkdir laravel-app/mysql

17. nano laravel-app/mysql/my.cnf

```php

18. put mysql configuration chnages in the .cnf file

	[mysqld]
	general_log = 1
	general_log_file = /var/lib/mysql/general.log
```

19.docker-compose up -d

20. docker ps

N.B : if web server container failed to boot up then please make sure your host apache or nginx port 80 should off

sudo service apache2 stop 







