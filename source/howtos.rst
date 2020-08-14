
HowTos
======

Clear container logs
--------------------

To clear the logs of a container your can::

    echo "" > $(docker inspect --format='{{.LogPath}}' <container_name_or_id>)

if the container is not running. Otherwise, you can configure docker to limit
the size of the logs as explained in `stackoverflow <https://stackoverflow.com/questions/42510002/how-to-clear-the-logs-properly-for-a-docker-container>`_.

Deploy a Swarm cluster
----------------------

Start the manager::

    docker swarm init

Connect the workers with the command provided by::

    docker swarm join-token worker

Create an overlay network::

    docker network create --driver overlay mynet


Start deploying your services or stacks::

    docker stack deploy -c mystack.yml mystack


Clean up swarm node
-------------------

Remove unused files::

    docker system prune

Leave swarm and remove the temp directory::

    docker swarm leave
    systemctl stop docker
    # recommended to also rm the node from the swarm
    rm -rf /tmp/docker/*
    systemctl stop docker
    # rejoin swarm

In the master node, the folder :file:`/var/lib/docker` might also contain
data that can be deleted.
