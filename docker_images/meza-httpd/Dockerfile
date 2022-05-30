FROM meza-rocky8-base:test
MAINTAINER Andy Rechenberg <github@rechenberg.net>

RUN dnf -y --setopt=tsflags=nodocs update --exclude=ansible* --exclude=python* && \
    dnf -y --setopt=tsflags=nodocs install httpd && \
    dnf clean all

EXPOSE 80

# Simple startup script to avoid some issues observed with container restart
ADD run-httpd.sh /run-httpd.sh
ADD php.conf.meza /etc/httpd/conf.d/php.conf
ADD meza-mw.conf /etc/httpd/conf.d/meza-mw.conf
ADD flux.php /var/www/html/flux.php
RUN chmod -v +x /run-httpd.sh

#FIXME - TEMPORARY - DELETE LATER
RUN mkdir -p /opt/htdocs && chown apache:apache /opt/htdocs
ADD flux.php /opt/htdocs

CMD ["/run-httpd.sh"]
