# Docker web server stack example

This project provides a framework and services to expose and administer container based website stacks.

The `host` folder contains the core stack used for docker admin and routing

The `example_site_X` folders contain examples of individual sites

## Host Services provided

* Reverse proxy (Traefik) to route urls to containers
* Docker admin UI (portainer)
* Database admin UI (phpmyadmin)

## Host Requirements

Host should have at a minimum

* docker
* docker-compose
* [ahoy](https://github.com/ahoy-cli/ahoy) (optional)
* mariadb (untested with containerised databases, but should be fine, db on host feels like a better approach)

## Install (Host)

* cd to `host` dir
* Ensure the user running the server is part of the `docker` group
* Copy .env.default to .env and update settings, especially passwords and urls
* Point any required DNS entries at host IP
* Run `docker-compose up -d`  or (`ahoy up` if using ahoy)
* Visit router/docker admin/database admin UI's to check all is working

## Admin UI's

* Visit the `ROUTER_HOST`, `DOCKER_UI_HOST` and `DB_UI_HOST` in your browser to view the admin UIs

## Add routing for new docker services (Sites)

Projects hosted on this server should all use docker (specifically docker-compose for grouping), exposing them to the outside world is done via the router.

To add a route, simply add labels to your services in the docker-compose, specifiying the host.

eg (docker-compose.yml):

```
version: '2.3'

services:

  nginx:
    image: nginx/nginx
    networks:
      - gateway
      - default
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.nginx.rule=Host(`website.example.local`)"
      - "traefik.http.services.nginx.loadbalancer.server.port=8080"
```

Note the host and port. This service will be accessible via `http://website.example.local` and connecting to port `8080` on this container.

Docs: https://docs.traefik.io/providers/docker/

## Authors

* Jeremy Graham