FROM php:7.4-apache

MAINTAINER Konrad Roziewski <kroziewski@gmail.com>

WORKDIR /var/www/html
RUN DEBIAN_FRONTEND=noninteractive apt update && apt install -y pkg-config nfdump rrdtool librrd-dev git
RUN a2enmod rewrite deflate headers expires
RUN pecl install rrd
RUN mv "$PHP_INI_DIR/php.ini-production" "$PHP_INI_DIR/php.ini"
RUN echo "extension=rrd.so" >> /usr/local/etc/php/conf.d/rrd.ini
RUN sed -e '/<Directory \/var\/www\/>/,/<\/Directory>/s/AllowOverride None/AllowOverride All/' -i /etc/apache2/apache2.conf

RUN git clone https://github.com/mbolli/nfsen-ng.git .
COPY settings.php backend/settings/settings.tmpl
COPY entrypoint.sh /tmp/

RUN printf '[PHP]\ndate.timezone = "Europe/Warsaw"\n' > /usr/local/etc/php/conf.d/tzone.ini
RUN printf 'admin:$apr1$A1iD8DJn$SAo1VLHzgV2eHtrbxmQiZ0' > /etc/apache2/.htpasswd
RUN printf 'AuthType Basic\nAuthName \"Your authorization required message.\"\\nAuthUserFile /etc/apache2/.htpasswd\nrequire valid-user' > /var/www/html/frontend/.htaccess
RUN chown -R www-data:www-data .
RUN chmod +x backend/cli.php /tmp/entrypoint.sh

ENV PROFILES_DATA_PATH  /var/nfdump/profiles-data/
ENV APACHE_RUN_USER     www-data
ENV APACHE_RUN_GROUP    www-data
ENV APACHE_LOG_DIR      /var/log/apache2
ENV APACHE_PID_FILE     /var/run/apache2.pid
ENV APACHE_RUN_DIR      /var/run/apache2
ENV APACHE_LOCK_DIR     /var/lock/apache2
ENV APACHE_LOG_DIR      /var/log/apache2

VOLUME [ "/var/nfdump/profiles-data" ]

EXPOSE 80

ENTRYPOINT [ "/tmp/entrypoint.sh" ]
