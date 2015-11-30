# Deployment

ClawIO is a service composed of various [micro services](http://martinfowler.com/articles/microservices.html).


Each micro service follows the [12 Factor App principles](http://12factor.net/) for configuration and management.

Each micro service is encapsulated into a Dockerfile.

To run the micro services the recommended way is to run them as Docker containers.

## Requirements
You have to have installed:

* [Docker](http://docs.docker.com/)
* [Docker Compose](http://docs.docker.com/compose/install/)


## Fast installation
```
curl --silent --show-error https://raw.githubusercontent.com/clawio/orches/master/install | sh
 ```