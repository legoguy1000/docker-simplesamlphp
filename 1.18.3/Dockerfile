FROM php:7.3-apache

ENV SIMPLESAMLPHP_RELEASE 1.18.3
WORKDIR /var/

# Update and install packages
RUN apt-get update && \
	apt-get install -y --no-install-recommends git libxml2-dev libzip-dev libldap2-dev libcurl4-gnutls-dev unzip libgmp-dev && apt-get clean && rm -rf /var/lib/apt/lists/*

RUN curl -Lo /var/simplesamlphp.tar.gz https://github.com/simplesamlphp/simplesamlphp/releases/download/v$SIMPLESAMLPHP_RELEASE/simplesamlphp-$SIMPLESAMLPHP_RELEASE.tar.gz \
 && tar xzf /var/simplesamlphp.tar.gz --directory /var \
 && mv /var/simplesamlphp-* /var/simplesamlphp \
 && touch /var/simplesamlphp/cert/breadcrumb \
 && touch /var/simplesamlphp/log/breadcrumb

RUN mkdir -p /var/lib/php/session/ && chmod a+t /var/lib/php/session/


# Install php pre-reqs
RUN docker-php-ext-install mysqli
RUN docker-php-ext-install mbstring
RUN docker-php-ext-install xml
RUN docker-php-ext-install zip
RUN docker-php-ext-install curl
RUN docker-php-ext-install json
RUN docker-php-ext-install pdo
#RUN docker-php-ext-install pdo_dblib
RUN docker-php-ext-install pdo_mysql
RUN docker-php-ext-install gmp
RUN docker-php-ext-install intl
RUN docker-php-ext-install ldap

# Apache Config
COPY apache.conf /etc/apache2/sites-available/000-default.conf
RUN mkdir /ssl_certs

# Configure the server
#HEALTHCHECK --timeout=5m --start-period=15s \
VOLUME ["/ssl_certs"]

# Enable apache mods
RUN a2enmod rewrite ssl
RUN a2ensite 000-default.conf

EXPOSE 80 443
COPY install-simplesamlphp.sh /var/install-simplesamlphp.sh
RUN chmod u+x /var/install-simplesamlphp.sh

COPY entrypoint /usr/local/bin/docker-php-entrypoint
RUN chmod +x /usr/local/bin/docker-php-entrypoint

ENTRYPOINT ["docker-php-entrypoint"]
CMD ["apache2-foreground"]
