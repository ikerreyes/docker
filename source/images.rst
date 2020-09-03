
Images
======


Images are read-only templates for containers that are composed of several layers.
Typically new images are build on top of existing ones.

Useful commands are::

	docker image ls # list images
	docker rmi <image> # remove image
	docker save <image> > <img>.tar # save image as tar


To publish and image to dockerhub::

	docker tag <local image[:tag]> <remote image[:tag]>
	docker push <remote image[:tag]>


If a non-existing base image needs to be created,
tools like `Deboostrap <https://wiki.debian.org/Debootstrap>`_
or `supermin <https://github.com/libguestfs/supermin>`_ are helpful.

Examples
--------

- `funbox <https://hub.docker.com/r/wernight/funbox>`_: fun images
