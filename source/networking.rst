
Networking
==========

Docker allows to use a variety of `drivers <https://docs.docker
.com/network/>`_ for networking:

- ``bridge``: used when your applications run in standalone containers that
  need to communicate. It is the default.
- ``host``: remove network isolation between the container and the Docker host
- ``overlay``: used to connect multiple Docker daemons together

Locally
-------

The easiest way to run Docker containers in your machine is to use the
``docker run`` command. By default they will use the ``docker0`` bridge network.

To make a container (with a DB, web...) visible to the other, there are 2
options:

- bind a local port to the exposed port in the container::

    docker run -p 8080:8080 -d web

  .. important:: Make sure your server is listening on all addresses, not
        only on localhost. To do so, use ``0.0.0.0``.

- connect them to the host network::

    docker run --network="host" -d web

  .. note:: This method does not allow you to map ports.

Another alternative is to use docker compose.

For a **production** usage, it is recommended to create a new bridge as it
allows *automatic service discovery* as explained in the `docker
documentation <https://docs.docker
.com/network/network-tutorial-standalone/#use-user-defined-bridge-networks>`_.


Swarm
-----

In a Docker Swarm cluster, it is recommended to use an *overlay* network, and
connect to it the containers that need to be exposed to the outside (e.g. ``-p
8080:80``) as explained in the `docker docs <https://docs.docker
.com/network/network-tutorial-overlay/#use-a-user-defined-overlay-network>`_.



