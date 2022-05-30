# Based on CentOS Dockerfile
#
# https://github.com/djflux/CentOS-Dockerfiles/blob/master/mariadb/centos7/Dockerfile
#
FROM meza-rocky8-base:test
MAINTAINER Andy Rechenberg <github@rechenberg.net>

RUN dnf -y update --exclude=ansible* --exclude=python* && \
    dnf -y install --setopt=tsflags=nodocs \
       mariadb-server \
       bind-utils \
       pwgen \
       psmisc \
       hostname \
       findutils && \ 
    dnf -y erase vim-minimal && \
    dnf clean all

# Fix permissions to allow for running on openshift
COPY fix-permissions.sh /
RUN /fix-permissions.sh /var/lib/mysql/   && \
    /fix-permissions.sh /var/log/mariadb/ && \
    /fix-permissions.sh /var/run/

COPY docker-entrypoint.sh /

ENTRYPOINT ["/docker-entrypoint.sh"]

# Place VOLUME statement below all changes to /var/lib/mysql
#VOLUME /var/lib/mysql

# By default will run as random user on openshift and the mysql user (27)
# everywhere else
USER 27

EXPOSE 3306
CMD ["mysqld_safe"]
