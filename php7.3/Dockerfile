FROM php:7.3-apache-buster

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
    apt-get -y --no-install-recommends install --fix-missing libcurl4 libcurl4-openssl-dev zip openssl libxslt-dev libxml2-dev && \
    rm -rf /var/lib/apt/lists/* && \
    curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer


# Install xdebug
# RUN pecl install xdebug-2.9.8 && \
#     docker-php-ext-enable xdebug

# Install redis
RUN pecl install redis-5.0.2 && \
    docker-php-ext-enable redis

# Install Imagick & Freetype
RUN apt-get -y update && \
    apt-get -y --no-install-recommends install --fix-missing libmagickwand-dev && \
    apt-get --no-install-recommends install -y libfreetype6-dev libjpeg62-turbo-dev libpng-dev && \
    rm -rf /var/lib/apt/lists/* && \
    pecl install imagick && \
    docker-php-ext-enable imagick && \
    docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ && \
    docker-php-ext-install -j$(nproc) gd

# Necessary PHP Extensions
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
    docker-php-ext-install exif && \
    docker-php-ext-install soap && \ 
    docker-php-ext-install bcmath && \ 
    docker-php-ext-install xsl && \ 
    docker-php-ext-install sockets && \ 
    docker-php-ext-install opcache

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

RUN echo "ServerName romanandjewels.docker" >> /etc/apache2/apache2.conf

USER $USER
RUN echo $(whoami)

RUN echo 'export PATH="$PATH:$HOME/.composer/vendor/bin"' >> ~/.bashrc

# Copy existing application directory permissions
COPY --chown=$USER:$USER . /var/www

# Set working directory
WORKDIR /var/www/html