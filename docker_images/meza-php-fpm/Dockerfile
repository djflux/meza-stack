FROM meza-rocky8-base:test

RUN dnf -y --setopt=tsflags=nodocs update --exclude=ansible* --exclude=python* && \
    dnf module reset php && \
    dnf module -y enable php:7.4 && \
    dnf -y --setopt=tsflags=nodocs install -y \
    php-bcmath   \
    php-cli      \
    php-common   \
    php-devel    \
    php-fpm      \
    php-gd       \
    php-intl     \
    php-json     \
    php-ldap     \
    php-mbstring \
    php-mysqlnd  \
    php-odbc     \
    php-opcache  \
    php-pdo      \
    php-process  \
    php-snmp     \
    php-soap     \
    php-xml      \
    php-xmlrpc   \
    && dnf clean all

RUN sed -e 's/127.0.0.1:9000/9000/' \
        -e '/allowed_clients/d' \
        -e '/catch_workers_output/s/^;//' \
        -e '/error_log/d' \
        -i /etc/php-fpm.d/www.conf

RUN mkdir -p /var/www/html

# Start php-fpm listening on a TCP port as it can be more
# performant
#RUN echo "listen = 9000" >> /etc/php-fpm.d/www.conf

ADD www.conf /etc/php-fpm.d/www.conf

# For some reason /run/php-fpm doesn't seem to be created during the
# "docker build" for this image. Make it ourselves
RUN mkdir -p /run/php-fpm

ENTRYPOINT /usr/sbin/php-fpm --nodaemonize
