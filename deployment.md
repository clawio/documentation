# Deployment

ClawIO is a service composed of various [micro services](http://martinfowler.com/articles/microservices.html).


Each micro service follows the [12 Factor App principles](http://12factor.net/) for configuration and management.

Each micro service is encapsulated into a Dockerfile.

To run the micro services the recommended way is to run them as Docker containers.

## Requirements
You have to have installed:

* [Docker](http://docs.docker.com/)
* [Docker Compose](http://docs.docker.com/compose/install/)



## Deployment script

Copy this script and execute it.

```
#!/usr/bin/bash

# In production you really want to change this variable 
CLAWIO_INSTALL_PATH=/tmp/clawioproject

mkdir -p CLAWIO_INSTALL_PATH
cd CLAWIO_INSTALL_PATH

# Clone required services. Add your own.
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

# Being paranoic ...
docker-compose up -d
docker-compose up -d
docker-compose up -d

# Show running containers. All should have state Up
docker-compose ps

```

After running the script the following services are running:


```
            Name                           Command               State            Ports
-------------------------------------------------------------------------------------------------
service.auth                    /bin/sh -c /go/bin/service ...   Up      0.0.0.0:57000->57000/tcp
service.elk                     /app/bin/start                   Up      0.0.0.0:9200->9200/tcp
service.localstore.data         /bin/sh -c /go/bin/service ...   Up      0.0.0.0:57002->57002/tcp
service.localstore.meta         /bin/sh -c /go/bin/service ...   Up      0.0.0.0:57001->57001/tcp
service.localstore.prop         /bin/sh -c /go/bin/service ...   Up      0.0.0.0:57003->57003/tcp
service.localstore.prop.mysql   /entrypoint.sh mysqld            Up      0.0.0.0:3306->3306/tcp
service.ocwebdav                /bin/sh -c /go/bin/service ...   Up      0.0.0.0:57004->57004/tcp
```

In the default installation there is one user created: username `demo` with password `demo`.

The first thing you want is to create a home directory for this user. The easiest way to do it is to use the ClawIO Command Line Interface program.

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
