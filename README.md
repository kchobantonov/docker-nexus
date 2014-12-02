# sonatype/docker-nexus

A busybox-based Docker image for Sonatype Nexus OSS with the Oracle JDK.

To build:

Copy the sources down and do the build-

```
# docker build --rm=true --tag=<username>/nexus
```

To run (if port 8081 is open on your host):

```
# docker run -d -p 8081:8081 --name nexus <username>/nexus
```

or to assign a random port that maps to port 8081 on the container:

```
# docker run -d -p 8081 --name nexus <username>/nexus
```

To determine the port that the container is listening on:

```
# docker ps nexus
```

To test:

```
$ curl http://localhost:8081/service/local/status
```

## Notes

* Installation of Nexus is to `/opt/sonatype/nexus`.  Notably:
  `/opt/sonatype/nexus-pro/conf/nexus.properties` is the properties file,
  which sets the context path to "/".

* A persistent directory, `/sonatype-work`, is used for configuration,
logs, and storage. This directory needs to be writable by the Nexus
process, which runs as UID 200.

* There are two general approaches to handling persistent storage requirements
with Docker. See [Managing Data in Containers](https://docs.docker.com/userguide/dockervolumes/) 
for additional information.

  1. *Use a data volume container*.  Since data volumes are persistent
  until no containers use them, a container can created specifically for 
  this purpose.  This is the recommended approach.  
  ```
    $ docker run -d --name nexus-data sonatype/nexus echo "data-only container for Nexus"  # create the data volume
    $ docker run -d -p 8081:8081 --name nexus --volumes-from nexus-data sonatype/nexus  # run nexus
  ```

  2. *Mount a host directory as the volume*.  This is not portable, as it
  relies on the directory existing with correct permissions on the host.
  However it can be useful in certain situations where this volume needs
  to be assigned to certain underlying storage.  
  ```
    # mkdir /some/dir/nexus-data && chown -R 200 /some/dir/nexus-data  # create the host directory
    # docker run -d -p 8081:8081 --name nexus -v /some/dir/nexus-data:/sonatype-work  # run nexus
  ```


## Oracle License

By using this container, you accept the Oracle Binary
Code License Agreement for Java SE available here:
http://www.oracle.com/technetwork/java/javase/terms/license/index.html