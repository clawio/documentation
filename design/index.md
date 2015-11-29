# Design

ClawIO design is highly influenced by the latest developments of file systems and object storages with a focus on high performance data handling, in particular by [CERN EOS project](http://eos.web.cern.ch/content/about-eos).

ClawIO architecture is also influenced by the architecture specification found on the [IETF Internet Sync Storage draft](https://datatracker.ietf.org/doc/draft-cui-iss-problem/?include_text=1).

## Key terms

ClawIO basic data unit is the **Resource**.

A Resource is one of these types:

* A **Container**: a resource that can contains others resources inside.

* An **Object**: a resource that contains binary data.


ClawIO provides an abstraction over local file systems and the terms folder and file are just implementations of such concepts.

Advanced architectures can be created on top of such concepts.

Even, it allow you to create your SpotifyFS, at the end, an album is a container of songs and songs are objects.

## Logical Units

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
ClawIO is shipped with a Authentication Unit that authenticates users against a SQLite3 database.

The Data unit is responsible for uploading and download binary data. Nothing else.

The Metadata Unit is responsible for handling a resource hierarchy and operating such hierarchy. Actions that involve the Metadata Unit include listing containers, creating containers, removing, moving and copying resources.

## Default architecture

The default architecture shipped with ClawIO is the following:

![Default Implemenentation](./default_implementation.png)

This architecture makes use of common technologies.


### Authentication Unit
The Authentication Unit is implemented with a SQLite3 database. Postgres, MySQL, MariaDB and FoundationDB can also be used changing a configuration parameter.

*Further developments will include LDAP/AD and Shibboleth implementations.*

### Data Unit

The Data Unit is implemented on top of a common filesystem like EXT4, EXT3, exFAT, NTFS and FAT. The object is saved **atomically** into a file. ClawIO has been designed with special care for data integrity and MD5, SHA1 and Adler32 checksums are supported out of the box to ensure data integrity in the transmission from a client.  Other checksums can be added with extreme simplicity.

*Further developments will include using OpenStack Swift/Amazon S3 and Ceph RadosGW as data units. Object*.

### Metadata Unit

The Metadata Unit is also implemented on top of a common filesystem. Such design choice is a **big difference** between ClawIO and ownCloud.

ownCloud creates the resource three inside a SQL database using a parent-child relationship and runs periodic sync jobs to maintain the state between the underlying filesystem and the SQL database. When files are being accessed behind the ownCloud Server, end users will not see the new data until the sync job has finished. As the number of users and resources increase, the load on the SQL database gets really higher.

ClawIO follows [CERNBox](http://cernbox.web.cern.ch/) design. The underlying filesystem is the only source of truth for the resource hierarchy. Such choice avoids the use of a sync job and the SQL database is just used to maintain file ids, Etags and modification times. 
With this design is possible to offer end users **Direct Access to the Storage**, so changes made via third-party tools to the filesystem will be presented to the user in real time. 

There is an alternative when ClawIO is deployed on top of a filesystem with support for extended attributes like EXT4. These filesystems can attach metadata to a file, so file ids can be kept along the file in the most **atomic** possible way. The SQL database is just delegated to a pseudo-KV store. This design is influenced by EOS, the underlying data backend that powers CERNBox.

*Further developments will discard the SQL database in favor of fast KV stores or High Performance databases.
Databases considered for prototyping are AeroSpike, Cassandra and HyperDex. There is a built-in prototype using AeroSpike.*

### Sync Unit
ClawIO main focus is to benchmark synchronisation protocols against a variety of data backends. 

The Sync Protocol shipped with ClawIO is the ownCloud Sync Protocol. ownCloud clients like the Desktop Sync Client can be connected to the ClawIO server to experiment different data backends.