.. Docker documentation master file, created by
   sphinx-quickstart on Wed Aug 12 13:29:27 2020.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to Docker's documentation!
==================================

Docker is a containerization software that allows to encapsulate applications.
In other words, it offers a system to create images and to run them in
an isolated environment.
It uses a client-server architecture.

Containers share OS and some libraries and/or with the host, unlike virtual
machines that hold their own OS.

.. toctree::
   :maxdepth: 2
   :caption: Contents:

   intro
   images
   containers
   dockerfile
   swarm
   networking


.. toctree::
   :maxdepth: 1
   :caption: Examples:

   examples/web
   examples/dockerflow


.. toctree::
   :maxdepth: 2
   :caption: Others:

   cmds
   howtos

To install docker in a Debian-based system::

   sudo apt install docker.io


Docker runs as a **root** daemon, that why it is so insecure.
To enable a *user* to execute docker commands without being root do::

   sudo groupadd docker
   sudo usermod -aG docker $USER
   newgrp docker
   # verify
   docker run hello-world


Other links
------------

- `A Makefile for your dockerfiles <https://philpep.org/blog/a-makefile-for-your-dockerfiles>`_

Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
