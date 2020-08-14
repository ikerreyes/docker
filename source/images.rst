
Images
======


Images are read-only templates for containers that are composed of several layers.

Useful commands are::

	docker image ls # list images
	docker rmi <image> # remove image
	docker save <image> > <img>.tar # save image as tar


To publish and image to dockerhub::

	docker tag <local image[:tag]> <remote image[:tag]>
	docker push <remote image[:tag]>
