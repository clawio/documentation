# Deployment

ClawIO is a service composed of various [micro services](http://martinfowler.com/articles/microservices.html).


Each micro service follows the [12 Factor App principles](http://12factor.net/) for configuration and management.

Each micro service is encapsulated into a Dockerfile.

To run the micro services the recommended way is to run them as Docker containers.


## Micro services

These are the micro services that compose the ClawIO project:

* service.auth: handles authentication 

The bootstrap of the ClawIO project is done by bootstraping a set of micro services.