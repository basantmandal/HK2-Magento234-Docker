FROM php:7.4.2-apache-buster

# Surpresses debconf complaints of trying to install apt packages interactively
# https://github.com/moby/moby/issues/4032#issuecomment-192327844

ARG DEBIAN_FRONTEND=noninteractive

# Update
RUN apt-get -y update --fix-missing && \
    apt-get upgrade -y && \
    apt-get --no-install-recommends install -y apt-utils && \
    rm -rf /var/lib/apt/lists/*


# Install useful tools and install important libaries
RUN apt-get -y update && \
    apt-get -y --no-install-recommends install nano wget dialog libsqlite3-dev libsqlite3-0 && \
    apt-get -y --no-install-recommends install mariadb-client zlib1g-dev libzip-dev libicu-dev && \
    apt-get -y --no-install-recommends install --fix-missing apt-utils build-essential git curl && \ 
    apt-get -y --no-install-recommends install --fix-missing libcurl4 libcurl4-openssl-dev zip openssl && \
    rm -rf /var/lib/apt/lists/* && \
    curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Install xdebug
# RUN pecl install xdebug-2.9.8 && \
#     docker-php-ext-enable xdebug

#  Opcache
RUN docker-php-ext-install opcache
# ADD opcache.ini "$OP_CACHE_INI"

# Install redis
RUN pecl install redis-5.0.2 && \
    docker-php-ext-enable redis

# Install imagick
RUN apt-get -y update && \
    apt-get -y --no-install-recommends install --fix-missing libmagickwand-dev && \
    rm -rf /var/lib/apt/lists/* && \
    pecl install imagick && \
    docker-php-ext-enable imagick

# Other PHP7 Extensions
RUN docker-php-ext-install pdo_mysql && \
    docker-php-ext-install pdo_sqlite && \
    docker-php-ext-install mysqli && \
    docker-php-ext-install curl && \
    docker-php-ext-install tokenizer && \
    docker-php-ext-install json && \
    docker-php-ext-install zip && \
    docker-php-ext-install -j$(nproc) intl && \
    docker-php-ext-install gettext && \
    docker-php-ext-install calendar && \
    docker-php-ext-install exif


# Install Freetype 
RUN apt-get -y update && \
    apt-get --no-install-recommends install -y libfreetype6-dev \
    libjpeg62-turbo-dev \
    libpng-dev && \
    rm -rf /var/lib/apt/lists/* && \
    docker-php-ext-configure gd --enable-gd --with-freetype --with-jpeg && \
    docker-php-ext-install gd

# Magento 2.3.6 Specific Extensions
RUN apt-get update -y \
    && apt-get install -y \
    libxslt-dev libxml2-dev

RUN docker-php-ext-install soap && \ 
    docker-php-ext-install bcmath && \ 
    docker-php-ext-install xsl && \ 
    docker-php-ext-install sockets
# ---------------------------

RUN echo "GIT Version:" && git --version
RUN echo "Curl Version:" && curl --version
RUN echo "wget Version:" && wget --version

RUN echo "ServerName localhost" >> /etc/apache2/apache2.conf

# Enable apache modules
RUN a2enmod rewrite headers

# Restart Apache Service
RUN service apache2 restart

# Cleanup
RUN rm -rf /usr/src/*


# Create User & Add to Specific Groups
ARG USER
ARG UID 
RUN useradd -G www-data,root -u $UID -d /home/$USER $USER || true

# Allow Created User to use Composer & Artisan Commands
RUN mkdir -p /home/$USER/.composer && \
    chown -R $USER:$USER /home/$USER

USER $USER
RUN echo $(whoami)


# Laravel Specific Updates
# RUN composer global require laravel/installer
RUN echo 'export PATH="$PATH:$HOME/.composer/vendor/bin"' >> ~/.bashrc

# Copy existing application directory permissions
COPY --chown=$USER:$USER . /var/www

# Set working directory
WORKDIR /var/www/html

