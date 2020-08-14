
DockerFlow
==========

Use DockerFlow in a Swarm cluster to support websites.


.. code-block:: yaml

    version: "3"

    services:

      proxy:
        image: dockerflow/docker-flow-proxy
        ports:
          - 80:80
          - 443:443
        networks:
          - proxy
        environment:
          - LISTENER_ADDRESS=swarm-listener
          - TIMEOUT_SERVER=2400
          - TIMEOUT_HTTP_KEEP_ALIVE=50
          - TIMEOUT_HTTP_REQUEST=30
          - TIMEOUT_CONNECT=30
          - TIMEOUT_CLIENT=60
          - COMPRESSION_ALGO=gzip
          - MODE=swarm
        secrets:
          - cert_www_mysite_com
        deploy:
          replicas: 1
          placement:
            constraints: [node.role == manager]

      swarm-listener:
        image: dockerflow/docker-flow-swarm-listener
        networks:
          - proxy
        volumes:
          - /var/run/docker.sock:/var/run/docker.sock
        environment:
          - DF_NOTIFY_CREATE_SERVICE_URL=http://proxy:8080/v1/docker-flow-proxy/reconfigure
          - DF_NOTIFY_REMOVE_SERVICE_URL=http://proxy:8080/v1/docker-flow-proxy/remove
        deploy:
          placement:
            constraints: [node.role == manager]

    secrets:
      cert_www_mysite_com:
        file: /docker/certs/www_mysite_com.pem
        external: true

    networks:
      proxy:
        external: true

The HAProxy will take care of encrypting the traffic to the outside, while
the traffic between the HAProxy and the server running ``mysite.com`` goes on
HTTP.

.. note::

    The ``swarm-listener`` service needs access to the docker socket to be able
    to discover new services.

In this setup, any service connected to the ``proxy`` overlay network will be
reachable. To be able to reach it, you need label the services specially,
for example:

.. code-block:: yaml

    version: "3"
    services:

      web:
        image: myweb
        networks:
          - proxy
          - default
        deploy:
          replicas: 1
          labels:
            - com.df.notify=true
            - com.df.serviceDomain=www.mysite.com,mysite.com
            - com.df.servicePath=/
            - com.df.aclName=99-mysite
            - com.df.port=8080
            - com.df.httpsOnly=true

    networks:
      proxy:
        external: true
      default:

The ``aclName`` is used to sort the containers that should respond to a
particular ``serviceDomain``. If the first container (given the name) is not
the appropriate to respond (because the service path is not the one), then it
goes to the next one and so on.

