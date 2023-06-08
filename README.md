# Traefik

![docker](https://github.com/TrouveTaVoie/traefik/assets/74034851/108a4341-e83c-4768-b317-f70ba067e29b)
[Traefik](https://traefik.io) is a modern HTTP reverse proxy and load balancer that makes deploying microservices easy.

Traefik integrates with your existing infrastructure components ([Docker](https://www.docker.com/), [Swarm mode](https://docs.docker.com/engine/swarm/), [Kubernetes](https://kubernetes.io), [Marathon](https://mesosphere.github.io/marathon/), [Consul](https://www.consul.io/), [Etcd](https://coreos.com/etcd/), [Rancher](https://rancher.com), [Amazon ECS](https://aws.amazon.com/ecs), ...) and configures itself automatically and dynamically.





# swarmMode

## Create acme.json file
Create the acme.json, to store letsencrypt certificates used by traefik, and lock down the permissions so that only the owner of the file has read and write permission:

```
sudo touch acme.json
sudo chmod 600 acme.json
```

## Create the traekik.yml file
```
sudo touch traefik.yml
```

## Brief description of traefik.yml

Traefik is the service being deployed.<br>
It uses the Traefik v2.0 image.<br>
It exposes an API with insecure access enabled.<br>
The Docker provider is used to dynamically configure routing based on Docker containers.<br>
Traefik listens on ports 80, 443, and 8080.<br>
The Traefik dashboard is enabled and accessible via traefik.marketfiler.com.<br>
Basic authentication is enabled for the Traefik dashboard.<br>
There is a catch-all router that redirects HTTP traffic to HTTPS.<br>
The Traefik container is connected to the public network (an external network that exists outside the Docker Compose file).<br>
Volumes are mounted for Docker socket access and storing ACME certificates.<br>


## Labels to use in docker-file of services
A labels section should be added to the docker-compose.yml file of the services to be routed through traefik:<br>
-**front**:
```
labels:
    - "traefik.enable=true"
    - "traefik.docker.network=public"
    - "traefik.http.routers.front.rule=Host(`front-domaine-name`)"
    - "traefik.http.routers.front.tls.certresolver=default"
    - "traefik.http.routers.front.entrypoints=websecure"
    - "traefik.http.services.front.loadbalancer.server.port=80"
```
-**api**:
```
labels:
    - "traefik.enable=true"
    - "traefik.docker.network=public"
    - "traefik.http.routers.api.rule=Host(`api-doamine-name`)"
    - "traefik.http.routers.api.tls.certresolver=default"
    - "traefik.http.routers.api.entrypoints=websecure"
    - "traefik.http.services.api.loadbalancer.server.port=3000"
```
:pushpin: Notice that we declare the 'public' network at the end of the lago docker-compose.yml:<br>
```
networks:
  public:
    external: true
```
:pushpin: Dont forget to connect the services in your docker compose file to the 'public' network, via :<br>
```
networks:
  - public
```





## Deploy traefik  docker swarm mode:
```
docker stack deploy -c traefik.yml traefik
```

