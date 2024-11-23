FROM php:8.3-fpm-alpine

# Install packages
RUN apk update
RUN apk add --no-cache curl libcurl git build-base zlib-dev oniguruma-dev autoconf bash icu-dev
RUN docker-php-ext-configure intl && docker-php-ext-install intl
RUN apk add --update linux-headers
RUN apk add --update libpng-dev libwebp-dev jpeg-dev libjpeg-turbo-dev freetype-dev
RUN apk add --update libheif libheif-dev libheif-tools vips-dev vips

# install additional packages from PECL
RUN apk add --no-cache libzip-dev zip
RUN pecl install libzip-dev zip && docker-php-ext-enable zip
RUN pecl install msgpack && docker-php-ext-enable msgpack

RUN pecl install igbinary
RUN docker-php-ext-enable igbinary

RUN docker-php-ext-configure gd --with-jpeg --with-webp
RUN docker-php-ext-install gd
RUN docker-php-ext-enable gd
RUN docker-php-ext-install exif
RUN docker-php-ext-install mysqli

# Redis
RUN pecl install redis && docker-php-ext-enable redis
RUN pecl install excimer

# Xdebug
ARG INSTALL_XDEBUG=false
RUN if [ ${INSTALL_XDEBUG} = true ]; \
    then \
      pecl install xdebug && docker-php-ext-enable xdebug; \
    fi;

COPY ./docker/php-fpm/php.ini /usr/local/etc/php/php.ini
COPY ./docker/php-fpm/conf.d/xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini

# opcache
RUN docker-php-ext-install opcache
COPY docker/php-fpm/conf.d/opcache.ini /usr/local/etc/php/conf.d/opcache.ini

# uploads sizes
COPY docker/php-fpm/conf.d/uploads.ini /usr/local/etc/php/conf.d/uploads.ini

#logs
COPY docker/php-fpm/conf.d/logs.ini /usr/local/etc/php/conf.d/logs.ini

#date
COPY ./docker/php-fpm/conf.d/date.ini /usr/local/etc/php/conf.d/date.ini

#php-fpm www.conf
COPY ./docker/php-fpm/www.conf /usr/local/etc/php-fpm.d/www.conf

# Postgres
RUN apk add --no-cache libpq-dev && docker-php-ext-install pdo_pgsql

#FFMpeg
RUN apk add --update --no-cache ffmpeg

#ImageMagick
RUN apk add --no-cache \
        imagemagick-dev \
        imagemagick \
        libtool \
        autoconf \
        g++ \
        make \
        pkgconf \
        bash \
        linux-headers

RUN pecl bundle imagick && \
    cd imagick && \
    phpize && \
    ./configure && \
    make && \
    make install && \
    docker-php-ext-enable imagick

RUN apk del --no-cache \
        imagemagick-dev \
        libtool \
        autoconf \
        g++ \
        make \
        pkgconf && \
    rm -rf /tmp/pear

# Nginx
#RUN apk add --update --no-cache nginx
#COPY ./docker/php-fpm/nginx.conf /etc/nginx/
#RUN chown -Rf www-data:www-data /var/lib/nginx

# Supervisor
#RUN apk add --no-cache supervisor
#COPY ./docker/php-fpm/supervisord.conf /etc/supervisord.conf

# Configure non-root user.
ARG PUID=1000
ARG PGID=1000
RUN apk --no-cache add shadow && \
    groupmod -o -g ${PGID} www-data && \
    usermod -o -u ${PUID} -g www-data www-data

# Source code
RUN chown -R www-data:www-data /var/www
#RUN find /var/www ! -path '*vendor*' | xargs -I {} chown www-data:www-data {}
#COPY --chown=www-data:www-data ./ /var/www
WORKDIR /var/www

# Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer
#ARG BUILD_MODE=dev
#RUN if [ ${BUILD_MODE} = dev ]; \
#    then \
#      composer install --no-interaction; \
#    else \
#      composer install --no-dev --no-interaction --no-progress --no-scripts --optimize-autoloader; \
#    fi;
RUN chown -R www-data:www-data /var/www/

CMD php-fpm

#EXPOSE 8080
EXPOSE 9000

#CMD ["/bin/sh", "./docker/php-fpm/run.sh"]