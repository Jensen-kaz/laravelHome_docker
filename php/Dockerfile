FROM php:7.1-fpm

ARG dev_mode

RUN if [ "$dev_mode" = true ]; then echo "\033[34mDev mode\033[m"; else echo "\033[32mProduction mode\033[m"; fi

RUN apt-get update \
    && apt-get -y install \
	git \
    zlib1g-dev \
    procps \
    curl \
    libpng-dev \
    wget \
    vim \
    default-mysql-client \
    sudo \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

RUN ln -s /usr/local/bin/php /usr/bin/php


RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"\
    && php -r "if (hash_file('sha384', 'composer-setup.php') === $(wget -q -O - https://composer.github.io/installer.sig)) { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"\
    && php composer-setup.php\
    && php -r "unlink('composer-setup.php');"\
    && mv composer.phar /usr/local/bin/composer

RUN if [ "$dev_mode" = true ]; then curl -sL https://deb.nodesource.com/setup_10.x | bash - && apt-get install -y nodejs \
       && yes | pecl install xdebug \
       && echo "zend_extension=$(find /usr/local/lib/php/extensions/ -name xdebug.so)" > /usr/local/etc/php/conf.d/xdebug.ini \
       && echo "xdebug.remote_enable = 1" >> /usr/local/etc/php/conf.d/xdebug.ini \
       && echo "xdebug.remote_host = 192.168.220.1" >> /usr/local/etc/php/conf.d/xdebug.ini \
       && echo "xdebug.remote_autostart = 1" >> /usr/local/etc/php/conf.d/xdebug.ini; fi

RUN docker-php-ext-install \
	zip \
    pdo_mysql \
    mysqli \
    calendar \
 	pcntl \
    sockets


ARG userid
ARG username

RUN useradd --no-log-init --no-create-home --user-group --shell /bin/bash -u $userid  $username
RUN usermod -a -G www-data $username

EXPOSE 9000

WORKDIR /var/www/

CMD ["php-fpm"]
