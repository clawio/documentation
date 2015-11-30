# Deployment

ClawIO is a service composed of various [micro services](http://martinfowler.com/articles/microservices.html).


Each micro service follows the [12 Factor App principles](http://12factor.net/) for configuration and management.

Each micro service is encapsulated into a Dockerfile.

To run the micro services the recommended way is to run them as Docker containers.

## Requirements
You have to have installed:

* [Docker](http://docs.docker.com/)
* [Docker Compose](http://docs.docker.com/compose/install/)


## Installation
```
curl --silent --show-error https://raw.githubusercontent.com/clawio/orches/master/install | sh
 ```

 
 If everything went correct, you should see the services up and running.
 
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

The services running are the following:

* **service.auth:** it is the authentication unit. It listens on port 57000 and speaks the gRPC protocol.
* **service.localstore.data**: it is the data unit. It listens on port 57002 and speaks pure HTTP through GET and PUT requests.
* **service.localstore.meta**: it is the meta unit. It listens on port 57001 and speaks the gRPC protocol.
* **service.localstore.prop**: it is a supporting component for the localstore implementation. It is responsible for file ids, Etags and modification times. It listens on port 57003 and speaks the gRPC protocol.
* **service.localstore.prop.mysql**: is the the MySQL database that powers the service.localstore.prop service.
* **service.ocwebdav:** it is the sync unit that implements the ownCloud sync protocol. It listens on port 57004 and speaks WebDAV.
* **service.localstore.prop.mysql**
* **service.elk**: it runs an ElasticSearch daemon for log recollection. The idea is to use Kibana to inspect logs across services.