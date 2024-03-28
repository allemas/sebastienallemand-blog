---
title: "Docker Commands - Toolbox"
tags: [devops, experiments]
date: 2020-01-18
description: Creation d'images docker, démarrage d'un container et gestion des network et volumes
author: Allemand Sébastien
---

# Dockerfile
Une image Docker est crée a partir d'un fichier Dockerfile représentant la structure du conteneur. On peut y trouver plusieurs éléments comme des librairies logicielles, une application, de la configuration, l'utilisation de volumes etc. Une fois cette image construire il sera possible de lancer plusieurs container basés sur la même image.
# Construire une image

```bash
docker build -t <docker_repository>/<image_name> .
```
Le `.` défini que le dockerfile est disposé dans le dossier courant.

# Lister les images docker disponible sur la machine
```bash
docker images
```
# Ignorer des fichiers dans une image docker avec dockerignore

Fichier  .dockerignore
_example:_
```bash
node_modules
npm-debug.log
Dockerfile
.dockerignore
```

---
# Conteneur Docker
Un conteneur est comme nous venons de le dire basé sur une image Docker, un conteneur comprend un système d'exploitation, du code applicatif, de la configuration middleware, et etc. Il peut être facilement déplacés et facilite grandement la gestion des infrastructures (surtout en développement).

# Demarrer un container
Note : Avant de démarrer un container il est necessaire de le construire (voir commande docker build)
```bash
docker run --name <container_name> -p <local_port>:<container_port> -d <docker_repository>/<image_name>
```
- `--name` spécifie le nom du container
- `-p <local_port>:<container_port>` permet de spécifier la correspondance des ports
- `-d` permet de démarrer le container en monde daemon (en tache de fond)

# Supprimer un container et son image
```bash
docker rm -v container_name
```

# Mode intéractif
Permet de d'exécuter un commande dans le container et d'intéragir avec. Souvent on utilise la commande pour ouvrir un shell.
```bash
docker exec -it <name_container> bash`
```
# Supprimer tous les containers inactifs
```bash
docker container prune
```
---

# Docker Network

Un Docker network est un réseau crée entre plusieurs conteneur. Il permet d'assurer la communiquer entre plusieurs conteneurs.
Créer un Docker Network permet de limiter les risques en isolant par le reseau les conteneurs et évite les conflits entre des conteneurs qui n'auraient pas a communiquer.

#  Créer un réseau
```bash
docker network create <name>
```

## Connecter un conteneur a un réseau
```bash
docker network connect <network_name> <container_name>
```
_When you create a new container, you can specify one or more --network flags. This example connects a Nginx container to the my-net network. It also publishes port 80 in the container to port 8080 on the Docker host, so external clients can access that port. Any other container connected to the my-net network has access to all ports on the my-nginx container, and vice versa._


## Network inspect
```bash
docker network inspect
```

### Networking container
Connected to bridge manualy
```bash
root@1f87bccac67c:/> cat /etc/hosts
127.0.0.1	localhost
::1	localhost ip6-localhost ip6-loopback
fe00::0	ip6-localnet
...
172.17.0.2	1f87bccac67c   <--
172.18.0.2	1f87bccac67c   <--
```

Pour aller plus loins :  [Basic networking](https://runnable.com/docker/basic-docker-networking)

---

# Docker Volumes
Un Docker Volume est un système de fichier, monté au démarrage d'un conteneur.
Un conteneur par design ne stocke **aucunes** données il ne dispose qu'uniquement des données en lecture seules.
Quand un conteneur modifie une donnée la donnée traverse les couches(layers) du conteneur puis cette donnée est modifiée dans le système de fichier monté au démarrage. C'est un volume.

### Creer un volume :
```bash
docker volume create <name>`
```
### Lister les volumes :
```bash
`docker volume ls `
```
### Supprimer les volumes non utilisés :
```bash
docker volume prune
```

## Monter un volume dans un conteneur docker
```bash
docker run --name snginx -p 8080:80 --mount source=my-vol,target=/var/www/html s/nginx
```

[how-to-build-a-node-js-application-with-docker-quickstart](https://www.digitalocean.com/community/tutorials/how-to-build-a-node-js-application-with-docker-quickstart)


# Différence entre conteneur et VM
![](./../../../assets/devops/docker-vm.png#center)

Un conteneur et une VM ont des aspects d'isolation similaires, mais leurs fonctionnement est différent la ou une VM virtualise les couches hardware un conteneur virtualise le système d'exploitation et sa configuration.
Un conteneur docker va uniquement contenir le code et les dépendances d'une applications la ou une machine virtuelle va démarrer une nouvelle machine(virtuelle) complètement indépendante.



