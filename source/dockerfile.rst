
Dockerfile
==========


A Dockerfile is a recipe file that can be used to create a docker image out
of it as::

	docker build <dir with Dockerfile> -t <name>


A Dockerfile contains a set of instructions, each of which constitutes
a new layer on top of the previous one.

Useful instructions in a dockerfile are:

.. code-block:: dockerfile

	FROM <base image> # base image
	ADD <file or dir> [<dst>] # copy files or directories to the image
	RUN <cmd> # execute command in the image
	ENV <var> # define environment variable
	CMD <cmd> # command to be executed when the container starts


Other useful instructions are:

.. code-block:: dockerfile

	WORKDIR <dir> # set a working directory
	VOLUME <path> # create a mount point for external volumes
	EXPOSE <port> # expose a particular port in the container
	USER <uid> # change container user


The **user** in the container is ``root`` by default. You can use any other
user, but it must exits in the host OS.
Many services use user ``999`` as default.


Example
-------

Example of a Dockerfile:

.. code-block:: dockerfile

	FROM ubuntu:18.04
	COPY . /app
	RUN make /app
	CMD python /app/app.py


Tips
----

Limit the number of instructions
********************************

Each instruction in a Dockerfile constitutes a new layer. For this reason, it
is often recommended to combine items that have the same instruction (if that
makes sense).
E.g. instead of

.. code-block::

	RUN apt-get update
	RUN apt-get install -y git


in two lines, they can be combined into a single line as:

.. code-block::

	RUN apt-get update && apt-get install -y git

In the :dk:`RUN` case, it also has other benefits, because the first
instruction is cached, it might be that the second gets and out of date
version of the packages.

Keep your image small
*********************

Try to use small base images (e.g. `alpine <https://hub.docker.com/_/alpine>`_)
or slim versions (e.g. `Debian bullseye-slim <https://github
.com/debuerreotype/docker-debian-artifacts/blob
/3503997cf522377bc4e4967c7f0fcbcb18c69fc8/bullseye/slim/Dockerfile>`_).

Moreover, try to remove any extra stuff you added and it is not required.
E.g.:

.. code-block:: dockerfile

	FROM ubuntu:18.04
	RUN apt-get update && apt-get install -y \
		build-essential \
		git \
	 && rm -rf /var/lib/apt/lists/*

