# HTTPS

Rappel: [[MY-DOMAIN]] doit être remplacé par votre domaine

**https seulement**
```yaml
# docker-composer.yaml

services:
  mon-service:
    image: php-apache
    ports:
    - 443
    labels:
    # Accès https
    - traefik.enable=true
    - traefik.http.routers.[[MY-SERVICE]]-https.entrypoints=secure
    - traefik.http.routers.[[MY-SERVICE]]-https.rule=Host(`[[MY-SERVICE]].[[MY-DOMAIN]]`)
    - traefik.http.routers.[[MY-SERVICE]]-https.tls=true
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

**http + https**
```yaml
# docker-composer.yaml

services:
  mon-service:
    image: php-apache
    ports:
    - 80
    - 443
    labels:
    # Activer Traefik
    - traefik.enable=true
    
    # Accès http
    - traefik.http.routers.[[MY-SERVICE]]-http.rule=Host(`[[MY-SERVICE]].[[MY-DOMAIN]]`)
    - traefik.http.routers.[[MY-SERVICE]]-http.entryPoints=insecure
    
    # Accès https
    - traefik.http.routers.[[MY-SERVICE]]-https.entrypoints=secure
    - traefik.http.routers.[[MY-SERVICE]]-https.rule=Host(`[[MY-SERVICE]].[[MY-DOMAIN]]`)
    - traefik.http.routers.[[MY-SERVICE]]-https.tls=true
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

**http + https avec redirection http -> https**
```yaml
# docker-composer.yaml

services:
  mon-service:
    image: php-apache
    ports:
    - 80
    - 443
    labels:
    # Activer Traefik
    - traefik.enable=true
    
    # Accès http
    - traefik.http.routers.[[MY-SERVICE]]-http.rule=Host(`[[MY-SERVICE]].[[MY-DOMAIN]]`)
    - traefik.http.routers.[[MY-SERVICE]]-http.entryPoints=insecure
    
    # Redirection http -> https
    - traefik.http.routers.[[MY-SERVICE]]-http.middlewares=https-redirect@docker
    
    # Accès https
    - traefik.http.routers.[[MY-SERVICE]]-https.entrypoints=secure
    - traefik.http.routers.[[MY-SERVICE]]-https.rule=Host(`[[MY-SERVICE]].[[MY-DOMAIN]]`)
    - traefik.http.routers.[[MY-SERVICE]]-https.tls=true
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
