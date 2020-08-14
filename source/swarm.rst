
.. _swarm:

Swarm
=====

Docker swarm is the cluster management system (similar to Kubernetes) for
Docker containers. It make use of stacks which are files that define how
services relate.

Docker Swarm runs services (containers). Those services can be grouped into
stacks.


Nodes
-----

Nodes are the different nodes (workers or managers in the swarm).

Useful commands are::

    docker node ls  # list nodes
    docker node rm <node> # remove a node


Stacks
------

Docker stacks are YAML files that indicate how the different containers are
built and, if needed, how they do relate.

Useful command are::

    docker stack deploy -c <file>.yml <name>
    docker stack rm <name>

The syntax is the same as for Docker Compose, but there are differences in
the available options.


.. code-block:: dockerfile

    version: "3"
    services:

      mongo:
        image: mongo:3.4
        networks:
          - default # only the internal network
        volumes:
          - /my/vol:/data/db
        deploy:
          replicas: 1

      web:
        image: myhub/myweb
        networks:
          - world  # external network to reachable
          - default # internal network to talk to the DB
        deploy:
          replicas: 1

    networks:
      world:
        external: true
      default:



Service
*******

There are a number of options than can be set to a service, but only
``image`` is required.


.. code-block:: dockerfile

    services:
      web:
        image: myhub/myweb  # base image
        networks:  # network the service has access to
          - world
          - default
        environment: # environment variables
          - MYVAR="some env var"
        working_dir: /app
        command: python web.py  # overwrite the default command
        deploy:
          replicas: 1
          labels: # useful identifiers
            - com.example.description: "My webapp"
          placement:
            constraints: [node.role == worker]
          resources:
            limits:
              cpus: '8.0'
              memory: 32G
            reservations:
              cpus: '4.0'
              memory: 8G


Services can be scaled up and down. By default, Docker Swarm performs load
balancing.

To scale a service simply run::

    docker service scale <service>=<replicas>


Network
*******

By default, containers in a stack can talk to each other if they are
connected to the same network. You can create a simple "internal" network by
adding a network to each service and to the ``networks`` part. In addition,
exposed ports by the containers are automatically exported to this "internal"
network, but can be remapped if needed.


.. code-block:: yaml

    version: "3"
    services:

      mongo:
        image: mongo:3.4
        networks:
          - default

      web:
        image: myhub/myweb
        ports:
          - "80:8080"
        networks:
          - default

    networks:
      default:

``default`` is just a placeholder, it can be any name.

Then, containers can see each other using a name that formed by the stack
name and the service name. E.g. if this stack is deployed as ``mystack`` (e.g
. :sh:`docker stack deploy -c mystack.yml mystack`), the web service can
reach the DB using the address ``mystack_mongo``.

.. note:: It should also be possible to connect to the service only using the
    name (e.g. ``mongo``).

Typically, for swarm clusters, an overly network is created::

    docker network create --driver overlay mynet

To connect a service to it, you only need to specify it in the networks
section and define it as external:


.. code-block:: dockerfile

    version: "3"
    services:

      mongo:
        image: mongo:3.4
        networks:
          - default # only the internal network

      web:
        image: myhub/myweb
        networks:
          - world  # external network to reachable
          - default # internal network to talk to the DB

    networks:
      world:
        external: true
      default:

Any port exposed by the ``web`` service (assuming it is configured to listen
on ``0.0.0.0``), will be reachable in the ``world`` network.

