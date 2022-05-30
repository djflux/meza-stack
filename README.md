# Sandbox for meza docker

This is a testing ground to getting meza to build an Enterprise Mediawiki
stack from separate Rocky Linux and other docker containers.

# Requirements

To run/test this repo you must have the following installed on your system:

* git
* ansible
* docker
* docker-compose

## Tested

The current repo was tested on Rocky Linux 8.6 with the following packages:

```
centos-release-ansible-29-1-2.el8.noarch
ansible-2.9.27-1.el8.noarch
git-2.31.1-2.el8.x86_64
docker-ce-rootless-extras-20.10.16-3.el8.x86_64
python3-docker-5.0.0-2.el8.noarch
docker-ce-cli-20.10.16-3.el8.x86_64
docker-ce-20.10.16-3.el8.x86_64
docker-scan-plugin-0.17.0-3.el8.x86_64
docker-compose-plugin-2.5.0-3.el8.x86_64
```

# How to build/test

If you have docker installed on your system you can test this repo by running
the following commands:

```
sudo git clone https://github.com/djflux/meza-stack.git /opt/meza-stack
cd /opt/meza-stack
ansible-playbook -i docker_images/inventory.ini docker_images/build.yml 
/usr/libexec/docker/cli-plugins/docker-compose -f stack.yml up -d
```

# What runs right now (30 May 2022)

If everything builds properly and the containers start there should be a running
web server and php-fpm container running. The web server is listening on :::8084
which is connected to the meza-httpd:80 container. The meza-php-fpm container is 
configured to listen on tcp 9000 and current has no access control restrictions
as it is assumed that the whole stack will only export necessary ports and the
rest of the containers can talk to each other on their own `meza` bridge network.

The meza-httpd container is configured to serve PHP via an Apache `ServerHandler` proxy
that connects by name to the meza-php-fpm container.

# Goals

The eventually goal is to integrate pieces of this repo into meza (https://github.com/enterprisemediawiki/meza). 
The vision is that each component of the meza stack is its own separate 
container. We can then rebuild another stack and use the load_balancer to
test a new SMW/MW/DB or any other component (other than the load_balancer :wink:)

## Changes to meza
These are the ideas for the docker commands for meza

### `meza docker build all`
Builds all of the required containers and installed all Enterprise MediaWiki components into the app_server images.

### `meza docker build <component>`
Rebuilds the specific component docker image (e.g. app_server, database_server, elastic_search_server, etc.)

### `meza docker compose up`
Starts the meza-stack. Builds the required images if they aren't already in the local docker repo.

Others?

