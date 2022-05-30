FROM rockylinux/rockylinux:8
MAINTAINER Andy Rechenberg <github@rechenberg.net>

RUN dnf -y --setopt=tsflags=nodocs update && \
    dnf -y --setopt=tsflags=nodocs install dnf-plugins-core && \
    dnf config-manager --set-enabled powertools && \
    dnf -y --setopt=tsflags=nodocs install epel-release && \
    dnf -y --setopt=tsflags=nodocs install centos-release-ansible-29 && \
    dnf -y --setopt=tsflags=nodocs install \
        python36 \
        git \
        ansible-2.9.27-1.el8.noarch \
        alternatives && \
    alternatives --set python /usr/bin/python3 && \
    dnf -y --setopt=tsflags=nodocs install \
        mariadb && \
    dnf clean all

EXPOSE 80

# Simple startup script to avoid some issues observed with container restart
#ADD run-httpd.sh /run-httpd.sh
#ADD php.conf.meza /etc/httpd/conf.d/php.conf
#ADD flux.php /var/www/html/flux.php
#RUN chmod -v +x /run-httpd.sh

#CMD ["/run-httpd.sh"]
