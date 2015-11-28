# Design

ClawIO design is highly influenced by the latest developments of file systems and object storages with a focus on high performance data handling, in particular by CERN EOS project.

ClawIO architecture is also influenced by the architecture specification found on the [IETF Internet Sync Storage draft](https://datatracker.ietf.org/doc/draft-cui-iss-problem/?include_text=1).


ClawIO is divided into four main logical units.


* Authentication unit
* Data unit
* Metadata unit
* Sync unit

*Further developments will include a Share unit.*

![Basic Design](basic_design.png)


## The Core

The ClawIO Core is composed of just three logical units: authentication, data and metadata unit.

The Authentication Unit is responsible for authenticating users against an Identity Provider.

## Default implementation

The default implementation shipped when installing ClawIO is the following:

![Default Implemenentation](./default_implementation.png)

Units:

* Authentication Unit: implemented using a SQLite3 database.

* Data Unit: implemented using commonn filesystems like EXT4, exFAT, XFS ...
* Meta Unit: implemented using common filesystems plus a MySQL database.
* 