FROM php:7.1-fpm-buster

RUN apt-get update && apt-get install -y \
    cron \
    git \
    gzip \
    libbz2-dev \
    libfreetype6-dev \
    libicu-dev \
    libjpeg62-turbo-dev \
    libmcrypt-dev \
    libpng-dev \
    libsodium-dev \
    libssh2-1-dev \
    libxslt1-dev \
    libzip-dev \
    lsof \
    default-mysql-client \
    vim \
    zip \
    procps

RUN docker-php-ext-configure \
    gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/

RUN docker-php-ext-install \
    bcmath \
    bz2 \
    calendar \
    exif \
    gd \
    gettext \
    intl \
    mbstring \
    mysqli \
    opcache \
    pcntl \
    pdo_mysql \
    soap \
    sockets \
    sysvmsg \
    sysvsem \
    sysvshm \
    xsl \
    zip

RUN pecl channel-update pecl.php.net \
    && pecl install xdebug

RUN docker-php-ext-enable xdebug \
    && sed -i -e 's/^zend_extension/\;zend_extension/g' /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini

## Replace next lines with below commented out version once issue is resolved
# https://github.com/php/pecl-networking-ssh2/pull/36
# https://bugs.php.net/bug.php?id=78560
RUN curl -o /tmp/ssh2-1.2.tgz https://pecl.php.net/get/ssh2 \
    && pear install /tmp/ssh2-1.2.tgz \
    && rm /tmp/ssh2-1.2.tgz \
    && docker-php-ext-enable ssh2
#RUN pecl install ssh2-1.2 \
#  && docker-php-ext-enable ssh2

RUN groupadd -g 1000 app \
    && useradd -g 1000 -u 1000 -d /var/www -s /bin/bash app

RUN apt-get install -y gnupg \
    && curl -sL https://deb.nodesource.com/setup_12.x | bash - \
    && apt-get install -y nodejs \
    && mkdir /var/www/.config /var/www/.npm \
    && chown app:app /var/www/.config /var/www/.npm \
    && chown app:app /usr/lib/node_modules \
    && npm install -g grunt-cli \
    && npm install url2 \
    && npm install spritesmith \
    && npm install spritesheet-templates

RUN curl -sSLO https://github.com/mailhog/mhsendmail/releases/download/v0.2.0/mhsendmail_linux_amd64 \
    && chmod +x mhsendmail_linux_amd64 \
    && mv mhsendmail_linux_amd64 /usr/local/bin/mhsendmail

RUN curl -sS https://getcomposer.org/installer | \
    php -- --version=1.9.0 --install-dir=/usr/local/bin --filename=composer

RUN curl -s https://packages.blackfire.io/gpg.key | apt-key add - \
    && echo "deb http://packages.blackfire.io/debian any main" | tee /etc/apt/sources.list.d/blackfire.list \
    && apt-get update \
    && apt-get install blackfire-agent blackfire-php

RUN printf '* *\t* * *\tapp\t%s/usr/local/bin/php /var/www/html/update/cron.php\n' >> /etc/crontab \
    && printf '* *\t* * *\tapp\t%s/usr/local/bin/php /var/www/html/bin/magento cron:run\n' >> /etc/crontab \
    && printf '* *\t* * *\tapp\t%s/usr/local/bin/php /var/www/html/bin/magento setup:cron:run\n#\n' >> /etc/crontab

COPY conf/www.conf /usr/local/etc/php-fpm.d/
COPY conf/php.ini /usr/local/etc/php/
COPY conf/php-fpm.conf /usr/local/etc/
COPY bin/cronstart /usr/local/bin/

## Added external ssh key to docker
# RUN mkdir /var/www/.ssh && ln -s /run/secrets/host_ssh_key /var/www/.ssh/id_rsa

RUN mkdir -p /var/www/html /var/www/html/project /sock \
    && chown -R app:app /var/www /var/www/html /var/www/html/project /usr/local/etc/php/conf.d /sock

USER app:app

VOLUME /var/www

WORKDIR /var/www/html

EXPOSE 9001