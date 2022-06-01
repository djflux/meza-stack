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


> **If there a service already listening on tcp/80 then the `docker-compose` 
> command will fail. Modify the `ports` section of `meza-httpd` in the `stack.yml`
> file to use ports that are available on your container host. Also make sure
> to open the appropriate firewall ports that you specify. ALWAYS check with 
> your IT admin before opening firewall ports :wink: **

# What runs right now (01 June 2022)

If everything builds properly and the containers start there should be haproxy,
Apache web server, and php-fpm (7.4) containers running. The stack.yml file
currently starts (3) `meza-httpd` replicas. Running `docker ps` should result
in something like the followin (container IDs and ephemeral TCP ports will be
different):

```
CONTAINER ID   IMAGE               COMMAND                  CREATED        STATUS        PORTS                                                                                  NAMES
df6fdba6f7b2   meza-haproxy:test   "/docker-entrypoint.…"   42 hours ago   Up 42 hours   0.0.0.0:80->80/tcp, :::80->80/tcp                                                      meza-stack-meza-haproxy-1
405d591a8d4d   meza-httpd:test     "/run-httpd.sh"          42 hours ago   Up 42 hours   0.0.0.0:49228->80/tcp, :::49228->80/tcp, 0.0.0.0:49226->8080/tcp, :::49226->8080/tcp   meza-stack-meza-httpd-3
fec4f0f74b67   meza-httpd:test     "/run-httpd.sh"          42 hours ago   Up 42 hours   0.0.0.0:49229->80/tcp, :::49229->80/tcp, 0.0.0.0:49227->8080/tcp, :::49227->8080/tcp   meza-stack-meza-httpd-1
c84afbf40801   meza-httpd:test     "/run-httpd.sh"          42 hours ago   Up 42 hours   0.0.0.0:49231->80/tcp, :::49231->80/tcp, 0.0.0.0:49230->8080/tcp, :::49230->8080/tcp   meza-stack-meza-httpd-2
f7472154c59f   meza-php-fpm:test   "/bin/sh -c '/usr/sb…"   42 hours ago   Up 42 hours   0.0.0.0:9090->9000/tcp, :::9090->9000/tcp                                              meza-stack-meza-php-fpm-1
```

There is an haproxy container listening on port 80 on the docker host 

> **The stack will fail to start with a `docker-compose up` command if there is 
> something already listening on tcp/80.**

The haproxy backend is configured with `server-template` so that can server
requests from up to six (6) meza-httpd Apache containers and forward them to
`meza-httpd:8080` by name using docker DNS. Since standard meza serves files
from `/opt/docs` there is a `meza-mw.conf` file placed into the `/etc/httpd/conf.d/`
directory inside the container upon build. This file configures a `VirtualHost`
that is listening on *:8080 and serving files from `/opt/htdocs`.

The `meza-httpd` images is configured to serve PHP via an Apache `ServerHandler`
proxy that connects by name to `meza-php-fpm:9000`

All communication between containers happens internally on docker network `meza`.

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

