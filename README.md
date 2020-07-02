# Dev env utils

Un reverse proxy basé sur Traefik.

Cela permet d'acceder à ses projets http/https en local en passant par une url du type http://mon-projet.mon-domain.

Dans cette documentation, [[MY-DOMAIN]] doit être remplacé par le domain locale souhaité (comme mylocal ou myname.kangourouge.com)

## Prérequis

- [Dnsmasq](./doc/dns_config.md) avec un domain sur lequel pointer en locale
- [Mkcert](./doc/mkcert.md) pour générer un certificat https (optionel, https)


## Installation

**Configurer le .env**

```bash
cp .env.dist .env
# remplace la valeur par défault par le domaine locale.
sed -i "" "s#CHANGE_ME#$[[MY-DOMAIN]]#g" .env
```

**Générer le certificat (optionel, https)**

```bash
mkcert -cert-file certs/default-cert.pem -key-file certs/default-key.pem "[[MY-DOMAIN]]" "*.[[MY-DOMAIN]]"
```

**Lancer les containers**

```bash
docker-compose up -d
```

## Usage

**Accéder au Dashbord de Traefik**
    

[Via localhost](localhost:8080)

ou   

traefik.[[MY-DOMAIN]]

**Accéder au Dashbord de Mailhog**
    
[Via localhost](localhost:8025)

ou 

mailhog.[[MY-DOMAIN]]

**Lier un projet avec docker-compose (générique)**

```yaml
# docker-composer.yaml

services:
  mon-service-1:
    image: php-apache
    ports:
    - 80
    labels:
    # Activer Traefik
    - traefik.enable=true
    
    # Accès http
    - traefik.http.routers.[[MY-SERVICE-1]]-http.rule=Host(`[[MY-SERVICE-1]].[[MY-DOMAIN]]`)
    - traefik.http.routers.[[MY-SERVICE-1]]-http.entryPoints=insecure
    
    # Requis
    networks:
    - proxy

  mon-service-2:
    image: nodejs
    ports:
    - 3000
    labels:
    - traefik.enable=true
    - traefik.http.routers.[[MY-SERVICE-2]]-http.rule=Host(`[[MY-SERVICE-2]].[[MY-DOMAIN]]`)
    - traefik.http.routers.[[MY-SERVICE-2]]-http.entryPoints=insecure
    networks:
    - proxy
    
    /.../
    
# Requis
networks:
  proxy:
    external:
      name: proxy
```

**Voir [ici](./doc/traefik_https.md) pour https et [ici](./doc/traefik_port.md) pour des problèmes de port**

**Aller plus loin**

La [documentation](https://docs.traefik.io/v2.2) de Traefik

## Limites connues

**sql:**  
Traefik est un reverse proxy http/https. 
Il ne peut donc pas fournir d'adresse pour une base de donnée sql comme postgres par exemple.

