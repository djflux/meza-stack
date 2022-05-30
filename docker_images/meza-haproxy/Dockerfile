FROM meza-rocky8-base:test
MAINTAINER Andy Rechenberg <github@rechenberg.net>

RUN dnf -y --setopt=tsflags=nodocs update --exclude=ansible* --exclude=python* && \
    dnf -y --setopt=tsflags=nodocs install haproxy openssl && \
    dnf clean all

# https://www.haproxy.org/download/1.8/doc/management.txt
# "4. Stopping and restarting HAProxy"
# "when the SIGTERM signal is sent to the haproxy process, it immediately quits and all established connections are closed"
# "graceful stop is triggered when the SIGUSR1 signal is sent to the haproxy process"
STOPSIGNAL SIGUSR1

RUN mkdir -p /var/lib/haproxy/ && chown haproxy:haproxy /var/lib/haproxy/

ADD meza-haproxy.cfg /etc/haproxy/haproxy.cfg

COPY docker-entrypoint.sh /
ENTRYPOINT ["/docker-entrypoint.sh"]

CMD ["haproxy", "-f", "/etc/haproxy/haproxy.cfg","-p","/run/haproxy.pid"]
