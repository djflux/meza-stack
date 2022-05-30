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
        ansible-2.9.27-1.el8 \
        alternatives && \
    alternatives --set python /usr/bin/python3 && \
    dnf clean all

