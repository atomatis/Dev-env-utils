# PORT

Rappel: [[MY-DOMAIN]] doit être remplacé par votre domaine

Si votre container ne dispose que d'un seul port ouvert, il va pointer automatiquement dessus.
Dans certain cas, votre container aura plusieurs ports disponibles.
Il faut alors rajouter une lgine pour lui indiquer sur quel port pointer.


```yaml
# docker-composer.yaml

services:
  mon-service:
    image: php-apache
    ports:
    - 1337
    - 2020
    labels:
    # Activer Traefik
    - traefik.enable=true
    
    # Accès http
    - traefik.http.routers.[[MY-SERVICE]]-http.rule=Host(`[[MY-SERVICE]].[[MY-DOMAIN]]`)
    - traefik.http.routers.[[MY-SERVICE]]-http.entryPoints=insecure
    
    # Pointe sur ip_container:1337
    - traefik.http.services.[[MY-SERVICE]]-http.loadbalancer.server.port=1337
    
    # Requis
    networks:
    - proxy

    /.../
    
# Requis
networks:
  proxy:
    external:
      name: proxy
```
