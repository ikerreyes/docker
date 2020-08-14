
.. _eg web app:

Web app
-------

In this example, we are going to run a cherrypy web application but leaving
the source code outside the image. The goal, is to be able to make changes in
the source code without rebuilding the image.

.. note:: The cherrypy server we are going to use by default automatically
	reloads itself when it detects that the application code has changed.

Create a new folder, and prepare a :file:`web.py` script and a
:file:`Dockerfile`.

The :file:`web.py` contains the cherrypy "hello world" example:

.. code-block:: python

	import cherrypy

	class HelloWorld(object):
		@cherrypy.expose
		def index(self):
			return "Hello World!"

	cherrypy.quickstart(HelloWorld())

and the :file:`Dockerfile` looks like:

.. code-block:: dockerfile

	FROM python:slim

	RUN pip install cherrypy

	# Create a mount point for the code
	VOLUME /app

	# Configure environment
	ENV PYTHONPATH=/app

	WORKDIR /app
	EXPOSE 8080
	CMD ["python", "web.py"]


Now the image can be built as::

	docker build <folder> -t web

and run it::

	docker run --name myweb -v <folder>:/app --network="host" -d web

.. note:: Alternatively, you can configure cherrypy to give access to anyone
    in the network (adding :code:`cherrypy.server.socket_host = '0.0.0.0'`)
    and then you only need to publish the port::

        docker run --name myweb -v <folder>:/app -p 8080:8080 -d web

Now you can browse to 127.0.0.1:8080 and get your message.
You can modify the ``Hello World!`` message, and the server will display the
new message once you reload.



Web app + DB
------------

The :ref:`web app example <eg web app>` can be complemented with a second
container that uses a DB. You can simply run a MongoDB as::

    docker run --name intogen_db -v $(DB_LOCATION):/data/db -p 27017:27017 -d mongo:4.2

The web app can connect to the database using ``localhost:27017``.

