# Design

ClawIO is divided into four main logical units.


* Authentication unit
* Data unit
* Metadata unit
* Sync unit

## Authentication unit.

This unit is responsible for authenticating  users against an identity provider.
 
A server need to implement the [authentication specification](https://github.com/clawio/specs/blob/master/auth/auth.proto) described below to become an authentication unit.

```
syntax = "proto3";

package auth;

service Auth {
    rpc Authenticate(AuthRequest) returns (AuthResponse) {}
}

message AuthRequest {
    string username = 1; // Users´s useranme
    string password = 2; // Users's password
}

message AuthResponse {
    string token = 1; // JWT token
}

```

ClawIO has already implemented an authentication unit that talks to a SQL database, being SQLite3 the default.


* Metadata service: provides rich information over binary data like path hierarchy and file identifiers.


ClawIO has been designed with 3 pilars in mind:

* Extensibility: the software has to be easily extendable.
* Composable: different resposanbilities are splitted across different componentes.
* Performance