FROM php:5.6-apache
MAINTAINER Stepan Mazurov <stepan@socialengine.com>

RUN DEBIAN_FRONTEND=noninteractive apt-get update && apt-get install -y \
	libmcrypt-dev \
    zlib1g-dev \
    telnet \
    git \
  && rm -rf /var/lib/apt/lists/*

# pdo_mysql
RUN docker-php-ext-install pdo_mysql

# mysqli
RUN docker-php-ext-install mysqli

# soap
RUN buildRequirements="libxml2-dev" \
	&& apt-get update && apt-get install -y ${buildRequirements} \
	&& docker-php-ext-install soap \
	&& apt-get purge -y ${buildRequirements} \
	&& rm -rf /var/lib/apt/lists/*

# yaml
RUN buildRequirements="libyaml-dev" \
	&& apt-get update && apt-get install -y ${buildRequirements} \
	&& pecl install yaml \
	&& echo "extension=yaml.so" > /usr/local/etc/php/conf.d/ext-yaml.ini \
	&& apt-get purge -y ${buildRequirements} \
	&& rm -rf /var/lib/apt/lists/*

COPY bin/* /usr/local/bin/

# Setup bare-minimum extra extensions for Laravel & others
RUN docker-php-ext-install mbstring mcrypt zip \
	&& docker-php-pecl-install \
	xdebug \
	&& rm ${PHP_INI_DIR}/conf.d/docker-php-pecl-xdebug.ini

# Install composer
RUN curl -sS https://getcomposer.org/installer | php -- \
	--install-dir=/usr/local/bin \
	--filename=composer

# You can use this to enable xdebug. start-apache2 script will enable xdebug
# if PHP_XDEBUG is set to 1
ENV PHP_XDEBUG 0
ENV PHP_TIMEZONE "UTC"

# The path that will be used to make Apache run under that user
ENV VOLUME_PATH /var/www/html/public

# Setup apache
RUN a2enmod rewrite

# Copy configs
COPY virtual-host.conf /etc/apache2/sites-available/000-default.conf
COPY xdebug.ini ${PHP_INI_DIR}/conf.d/docker-php-pecl-xdebug.ini.disabled
#COPY php.ini ${PHP_INI_DIR}/php.ini

# Setup App
RUN mkdir -p /app/public && chown -R www-data:www-data /app

WORKDIR /app

ENTRYPOINT ["setup-container"]

CMD ["apache2-foreground"]
