Base Docker image for SignalFuse container images
=================================================

*Note:* you actually cannot run `docker build` with this Dockerfile as
some of the software installed during the provisioning steps required
the build to run in privileged mode, which is not yet supported by
Docker.

The only way to build this image for now is to start a shell in the
`base` image, in privileged mode, and manually execute the steps (or
`docker insert` the provisioning script into a base image, execute it
and commit the result). When done, exit the container and commit the
container's state as an image.

```
$ docker run -i -t -privileged base bash
root@x:/# apt-get update
...
root@x:/# apt-get -y install ...
...
root@x:/# exit
$ docker commit <x> mpetazzoni/maestro-base
```

What's inside?
--------------

This base image provides a sane foundation for the other images created
and used by SignalFuse. It provides:

- Java 7, via `openjdk-7-jdk`;
- Docker, to have the client available to talk to Docker daemons from
  within a container;
- Pipework, for advanced container networking (if needed);
- Git and Wget, to download packages or repositories during provisioning
  of sub-images;
- Vim, for sane editing when one needs to drop into a shell inside the
  container.

Usage
-----

The most common way to use this base image is for Maestro orchestrated
containers. Start your `Dockerfile` with the following to get started:

```
FROM mpetazzoni/maestro-base

# Install Maestro for guest utils
RUN apt-get update
RUN apt-get -y install python python-setuptools
RUN easy_install http://github.com/signalfuse/maestro-ng/archive/master.zip

# Rest goes here
```

See http://github.com/signalfuse/maestro-ng/README.md for more
information on how to use Maestro and the guest utils (Python-based
helper functions for your container init script).
