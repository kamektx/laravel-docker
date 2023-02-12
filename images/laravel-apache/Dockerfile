FROM php:8.2-apache-bullseye

RUN echo "alias ls='ls --color=auto'" >> ~/.bashrc
RUN echo "alias la='ls -laF --group-directories-first --color=auto'" >> ~/.bashrc
RUN echo "alias grep='grep --color=auto'" >> ~/.bashrc
RUN echo "alias fgrep='fgrep --color=auto'" >> ~/.bashrc
RUN echo "alias egrep='egrep --color=auto'" >> ~/.bashrc

SHELL ["/bin/bash", "-oeux", "pipefail", "-c"] 

ENV TZ='Asia/Tokyo'
ENV COMPOSER_ALLOW_SUPERUSER=1
ENV COMPOSER_HOME=/composer

COPY ./php.ini /usr/local/etc/php/php.ini

RUN apt update \
    && apt install -y git libicu-dev libonig-dev libmongoc-dev zlib1g-dev  libzip-dev unzip locales nano iputils-ping net-tools sudo\
    && docker-php-ext-install intl opcache zip\
    && pecl install mongodb apcu\
    && docker-php-ext-enable mongodb apcu


COPY ./apache2.conf /etc/apache2/apache2.conf
COPY ./sites-available /etc/apache2/sites-available
RUN a2ensite 000-default
RUN a2enmod rewrite
RUN a2enmod alias

COPY --from=composer:latest /usr/bin/composer /usr/bin/composer
RUN composer global require "laravel/installer"
ENV PATH=/composer/vendor/bin:$PATH
RUN useradd -u 1000 capcom

RUN mkdir /laravel-api
RUN chown 1000:1000 /laravel-api
RUN chmod 777 /laravel-api
WORKDIR /laravel-api