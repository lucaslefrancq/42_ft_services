FROM alpine:latest

# installing php packages and nginx
RUN apk update && apk add vim php7-fpm php7-opcache php7-common php7-iconv php7-json php7-gd \
    php7-mysqli php7-imap php7-cgi fcgi php7-pdo php7-pdo_mysql php7-soap php7-xmlrpc \
    php7-posix php7-session php7-mcrypt php7-gettext php7-ldap php7-ctype php7-dom php7-curl \
    php7-xml php7-mbstring php7-cli php7-phar nginx mariadb-client

# installing wordpress
RUN mkdir -p /usr/share/webapp/wordpress/
RUN wget https://wordpress.org/latest.tar.gz
RUN tar xzf latest.tar.gz --strip-components=1 -C /usr/share/webapp/wordpress/
RUN rm latest.tar.gz
# for auto-install of wordpress with wp-cli
RUN wget https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar
RUN chmod +x wp-cli.phar & mv wp-cli.phar /usr/bin/wp-cli

# copying wordpress config file + rights
COPY wp-config.php /usr/share/webapp/wordpress/wp-config.php
RUN chmod 755 /usr/share/webapp/wordpress/wp-config.php
# replace default nginx conf with ours
RUN rm /etc/nginx/conf.d/default.conf
COPY my_nginx.conf /etc/nginx/conf.d/my_nginx.conf

# config file for php-fpm
RUN rm /etc/php7/php-fpm.d/www.conf
COPY www.conf /etc/php7/php-fpm.d/www.conf

# needed to launch nginx
RUN mkdir -p /run/nginx
# for launching php-fpm
RUN mkdir -p /run/php

# copying script for infinite loop to prevent exit
COPY script.sh /home/script/script.sh
# add 2 users to wordpress
COPY create_user_wp.sql /home/script/create_user_wp.sql

# giving rights for execution
RUN chmod 777 /home/script/script.sh

# container will start on this script
ENTRYPOINT /home/script/script.sh
