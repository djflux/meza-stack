FROM meza-rocky8-base:test
MAINTAINER Andy Rechenberg <github@rechenberg.net>

RUN yum -y --setopt=tsflags=nodocs update && \
    yum -y --setopt=tsflags=nodocs install httpd && \
    yum clean all

EXPOSE 80

# Simple startup script to avoid some issues observed with container restart
ADD run-httpd.sh /run-httpd.sh
ADD php.conf.meza /etc/httpd/conf.d/php.conf
ADD flux.php /var/www/html/flux.php
RUN chmod -v +x /run-httpd.sh

CMD ["/run-httpd.sh"]
