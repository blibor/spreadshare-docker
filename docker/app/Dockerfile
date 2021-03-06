# Pull base image
FROM phalconphp/php-apache:ubuntu-16.04

MAINTAINER Dennis Stücken <dstuecken@me.com>

ENV PROVISION_CONTEXT "production"

# Deploy scripts/configurations
COPY bin/*.sh /opt/docker/provision/entrypoint.d/

ENV COMPOSER_ALLOW_SUPERUSER 1
ENV COMPOSER_HOME /tmp
ENV COMPOSER_VERSION 1.5.2

RUN apt-get update && apt-get install -y curl

RUN curl -s -f -L -o /tmp/installer.php https://raw.githubusercontent.com/composer/getcomposer.org/da290238de6d63faace0343efbdd5aa9354332c5/web/installer \
 && php -r " \
    \$signature = '669656bab3166a7aff8a7506b8cb2d1c292f042046c5a994c43155c0be6190fa0355160742ab2e1c88d40d5be660b410'; \
    \$hash = hash('SHA384', file_get_contents('/tmp/installer.php')); \
    if (!hash_equals(\$signature, \$hash)) { \
        unlink('/tmp/installer.php'); \
        echo 'Integrity check failed, installer is either corrupt or worse.' . PHP_EOL; \
        exit(1); \
    }" \
 && php /tmp/installer.php --no-ansi --install-dir=/usr/bin --filename=composer --version=${COMPOSER_VERSION} \
 && composer --ansi --version --no-interaction \
 && rm -rf /tmp/* /tmp/.htaccess

RUN mkdir -p /vendor \
    && composer --working-dir=/vendor require phalcon/devtools \
    && ln -s /vendor/vendor/phalcon/devtools/phalcon.php /usr/local/bin/phalcon \
    && chmod ugo+x /usr/local/bin/phalcon \
    && chmod +x /opt/docker/provision/entrypoint.d/*.sh

RUN mkdir -p  /etc/apache2/spreadshare

 RUN echo "" >> /etc/apache2/apache2.conf \
    && echo "# Include spreadshare configurations from host machine" >> /etc/apache2/apache2.conf \
    && echo "IncludeOptional spreadshare/*.conf" >> /etc/apache2/apache2.conf

RUN apt-get autoremove -y \
    && apt-get autoclean -y \
    && apt-get clean -y
