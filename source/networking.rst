
Networking
==========

Docker uses the uses the `Container Network Model <https://github.com/docker/libnetwork/blob/master/docs/design.md>`_
and it allows to use a variety of `drivers <https://docs.docker
.com/network/>`_ for networking:

- ``null``: when no networking is required. Containers will not be accessible
  from outside.

- ``bridge``: used when your applications run in standalone containers that
  need to communicate. It is the default
  (containers on the same host are connected to the same ``docker0`` bridge).

  It uses a linux-specific implementation based on Linux Bridge,
  and forwards traffic between 2 networks based on MAC addresses.

- ``host``: remove network isolation between the container and the Docker host;
  so that containers share host's network namespace and thus
  containers have full access to the hosts network.

  Use ``--net=host`` in the docker un command.

- ``overlay``: used to connect multiple Docker daemons together.
  It provides multi-host communication over XVLAN (which uses ``libnetwork``).
  Container's IP packet is encapsulated inside host's packet when sending;
  and decapsulated and forward to the receiving container on the receiver host.

- ``macvlan``: each container has its own MAC address, so they can directly communicate.
  It requires hardware support.

- ``remote``: provide means of supporting drivers over a remote transport


To list available networks use
:sh:`docker network ls`.


Locally
-------

The easiest way to run Docker containers in your machine is to use the
``docker run`` command. By default they will use the ``docker0`` bridge network.

From a container, access to the outside network is done through source NAT.
But that cannot be used to connect containers on different hosts as
the private IP subnet of ``docker0`` bridge is not advertised or routed to the outside network beyond the host.

Docker uses a Destination NAT mechanism to allow communication between containers and
the outside network (or with containers sitting on other hosts).
Docker can allocate specific port numbers (TCP or UDP) on the host’s IP address,
and then forward or proxy the inbound communication to the respective containers.


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



Examples
--------


A ``bridge`` network can be created with::

    docker network create --driver bridge mybridge

and a container connected as::

    docker container run --net=mybridge -d --name=c1 busybox

Different containers can share network namespaces, and reach each other
by referring to ``localhost``:

    docker container run -it --name=c2 --net=container:c1 busybox /bin/sh

