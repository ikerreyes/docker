
Containers
==========


Containers are the runnable version build out of images::

	docker run debian /bin/echo ‘Hello world!’


Useful **options** for the ``run`` command are::

	-it # interactive mode
	-v <src>:<dst> # mount the <src> folder into <dst>
	--name <name> # provide
	-p <src>:<dst> # map a port from a container to the outside
	--network="host" # use the hosts network

Containers run while the command specified runs. Once that command finished,
the container is stopped. To keep a container running, you need to use the
**detach mode** (``-d``).

**Actions** that can be done are::

	docker start <container>
	docker stop <container>
	docker restart <container>


.. important:: If the container was ``run`` the first time with parameters
    like ``-v`` or ``-d`` (or any other), those are kept when you ``start``
    the container again.

On **running** containers you can::

	docker exec <container> <cmd> <opts> # execute a command
	docker logs <container>  # show logs of container
	docker inspect <container>

Containers can be **managed** as::

	docker ps # list running containers
	docker ps -a # list all containers
	docker rm <container>
	docker export <container> > <container>.tar

