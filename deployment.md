# Deployment

ClawIO is a service composed of various [micro services](http://martinfowler.com/articles/microservices.html).


Each micro service follows the [12 Factor App principles](http://12factor.net/) for configuration and management.

Each micro service is encapsulated into a Dockerfile.

To run the micro services the recommended way is to run them as Docker containers.

## Requirements
You have to have installed:

* [Docker](http://docs.docker.com/)
* [Docker Compose](http://docs.docker.com/compose/install/)


So you will need docker and docker-compose in your machine.


## Deployment script

Copy this script and execute it.

```
#!/usr/bin/bash

# In production you really want to change this variable 
CLAWIO_INSTALL_PATH=/tmp/clawioproject

mkdir -p CLAWIO_INSTALL_PATH
cd CLAWIO_INSTALL_PATH

git clone https://github.com/clawio/orches
git clone https://github.com/clawio/service.auth
git clone https://github.com/clawio/service.localstore.data
git clone https://github.com/clawio/service.localstore.meta
git clone https://github.com/clawio/service.ocwebdav
git clone https://github.com/clawio/service.localstore.prop

cp orches/docker-compose.yml .

# Ideally docker-compose up -d will be enough but given that
# some containers need to be launched in sequence and 
# compose does not guarantee that, we launch them in manually
# in the desired sequence.

docker-compose up -d --force-recreate service.auth
docker-compose up -d service.auth

docker-compose up -d --force-recreate service.localstore.prop.mysql
docker-compose up -d service.localstore.prop.mysql

docker-compose up -d --force-recreate service.localstore.prop
docker-compose up -d service.localstore.prop

docker-compose up -d --force-recreate service.localstore.meta
docker-compose up -d service.localstore.meta

docker-compose up -d --force-recreate service.localstore.data
docker-compose up -d service.localstore.data

docker-compose up -d --force-recreate service.ocwebdav
docker-compose up -d service.ocwebdav

# Being paranoic
docker-compose up -d
docker-compose up -d
docker-compose up -d

# Show running containers. All should have state Up
docker-compose ps

```

Before using the OwnCloud client you have to use the ClawIO CLI to setup your user:

 docker run -it --link "service.auth:service-auth" --link "service.localstore.data:service-localstore-data" --link "service.localstore.meta:service-localstore-meta" labkode/clawio bash
Once inside the container run:

clawio login demo demo
clawio home
clawio stat /local/users/d/demo --children
You can upload some files and create some directories:

clawio mkdir /local/users/d/demo/folderone
clawio upload /etc/passwd /local/users/d/demo/file
clawio upload /etc/passwd /local/users/d/demo/filewithchecksum --checksum="md5:$(md5sum /etc/passwd | cut -d ' ' -f1)"
clawio stat /local/users/d/demo --children
