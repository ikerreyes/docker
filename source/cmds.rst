
Summary of useful commands
==========================

Build a Dockerfile::

    docker build <folder> -t <image>

Run a bash shell using an image::

    docker run --name <name> -it <image> bash

Run a container forever and connect to it::

    docker run --name <name> -d <image>
    docker exec -it <name> bash

Run an existing container that was stopped (with the same parameters as in
the original run)::

    docker start <name>

Create a secret::

    docker secret create -c cert_www_mysite.com www_mysite_com.pem

Swarm
-----

Troubleshoot your services with::

    docker service ls # list services
    docker service logs -f <service> # see service logs
    docker service ps --no-trunc <service> # useful is service is not running

Monitor and debug
-----------------

Debug::

    docker inspect
    docker logs

Monitor::


    docker stats
    docker top

