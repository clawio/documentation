# Design

ClawIO design is highly influenced by the latest developments of file systems and object storages with a focus on high performance data handling, in particular by CERN EOS project.

ClawIO architecture is also influenced by the architecture specification found on the [IETF Internet Sync Storage draft](https://datatracker.ietf.org/doc/draft-cui-iss-problem/?include_text=1).


ClawIO is divided into four main logical units.


* Authentication unit
* Data unit
* Metadata unit
* Sync unit

Further developments will include a Share unit.

## Authentication unit.

This unit is responsible for authenticating  users against an identity provider.
 
A server needs to implement the [Auth Spec](https://github.com/clawio/specs/blob/master/auth/auth.proto) in order to become an authentication unit.

This unit is implemented with the gRPC framework.

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
ClawIO has already implemented an authentication unit that talks to a SQL database, being SQLite3 the default. See the Implementation Section for more details.


## Data unit.

This unit is responsible for uploading and downloading binary data. Nothing else.
 
A server needs to implement the [Data Spec](https://github.com/clawio/specs/blob/master/data/data.yaml) in order to become a data unit.

This unit is implemented with the HTTP protocol.

```

GET /<path>

Returns the binary contents of the object located at the specified path.

Succesful codes are:

* 200: OK

Possible error codes are:

 * 401: authentication needed
 * 403: permission denied
 * 404: resource does not exits
 * 415: download is not allowed on containers.
 
 Any error that does not fit into the previous set shoud return 500.
 

PUT /<path>[?checksum=type:payload]
[CIO-Checksum: type:payload]

[binary body]



Upload the contents of an object sent in the request body to the specfified path.

A checksum can be supplied via the checksum query param or via the CIO-Checksum header.

The format of the checksum has to be type:payload. For example:

md5:595f44fec1e92a71d3e9e77456ba80d1

Succesful codes are:

* 201: object created

Possible error codes are:

 * 401: authentication needed
 * 403: permission denied
 * 404: resource does not exits
 * 412: the provided checksum and the server computed checksum do not match

````

ClawIO has already implemented a data unit that uses a common file system (Ext4, Ext3, FAT ...) to save binary data. Further developments will include saving the data into CERN EOS and Object Storages like S3/Swift and Ceph (trough RadosGW). See the Implementation Section for more details.


## Metadata unit.

This unit is responsible for metadata operations.

A server needs to implement the [Meta Spec](https://github.com/clawio/specs/blob/master/metadata/metadata.proto) in order to become a metadata unit.


```
syntax = "proto3";

package metadata;

service Meta {
    rpc Home(HomeReq) returns (Void) {}
    rpc Mkdir(MkdirReq) returns (Void) {}
    rpc Stat(StatReq) returns (Metadata) {}
    rpc Cp(CpReq) returns (Void) {}
    rpc Mv(MvReq) returns (Void) {}
    rpc Rm(RmReq) returns (Void) {}
}

message Void {
}

message RmReq {
    string access_token = 1;
    string path = 2;
}

message MvReq {
    string access_token = 1;
    string src = 2;
    string dst = 3;
}

message HomeReq {
    string access_token = 1;    
}

message CpReq {
    string access_token = 1;
    string src = 2;
    string dst = 3;
}

message MkdirReq {
    string access_token = 1;
    string path = 2;
}

message StatReq {
    string access_token = 1;
    string path = 2;
    bool children = 3;
}

message Metadata {
    string id = 1;
    string path = 2;
    uint32 size = 3;
    bool is_container = 4;
    string mime_type = 5;
    string checksum = 6;
    uint32 modified = 7;
    string etag = 8; 
    uint32 permissions = 9;
    repeated Metadata children = 10;
}
```

ClawIO has already implemented a metadata unit that uses both a file system to create the path hierarchy and a MySQL database to save additional information about the data. See the Implementation Section for more details.
