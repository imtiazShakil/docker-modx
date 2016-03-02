FROM php:5.6-apache

MAINTAINER Vadim Homchik <homchik@gmail.com> (@vh)

RUN a2enmod rewrite

# install the PHP extensions we need
RUN apt-get update && apt-get install -y libpng12-dev libjpeg-dev unzip && rm -rf /var/lib/apt/lists/* \
	&& docker-php-ext-configure gd --with-png-dir=/usr --with-jpeg-dir=/usr \
	&& docker-php-ext-install gd mysqli opcache mbstring pdo pdo_mysql xml zlib

# set recommended PHP.ini settings
# see https://secure.php.net/manual/en/opcache.installation.php
RUN { \
		echo 'opcache.memory_consumption=128'; \
		echo 'opcache.interned_strings_buffer=8'; \
		echo 'opcache.max_accelerated_files=4000'; \
		echo 'opcache.revalidate_freq=60'; \
		echo 'opcache.fast_shutdown=1'; \
		echo 'opcache.enable_cli=1'; \
	} > /usr/local/etc/php/conf.d/opcache-recommended.ini

VOLUME /var/www/html

ENV MODX_VERSION 2.4.3
ENV MODX_SHA1 0

# upstream tarballs include ./modx-${MODX_VERSION}/
RUN curl -o modx.zip -SL http://modx.com/download/direct/modx-${MODX_VERSION}-pl.zip \
	&& echo "$MODX_SHA1 *modx.zip" | sha1sum -c - \
	&& unzip modx.zip -d /usr/src \
  && mv /usr/src/modx-${MODX-VERSION}-pl /usr/src/modx \
  && find /usr/src/modx -name 'ht.access' -exec bash -c 'mv $0 ${0/ht.access/.htaccess}' {} \; \
  && rm modx.zip \
	&& chown -R www-data:www-data /usr/src/modx

COPY docker-entrypoint.sh /entrypoint.sh

ENTRYPOINT ["/entrypoint.sh"]
CMD ["apache2-foreground"]