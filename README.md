# Dproxy

Basé sur le [repo éponyme](https://gitlab.smol.fr/smol/dproxy/-/tree/traefik) de Vixns

Un reverse proxy basé sur Traefik.

Cela permet d'acceder à ses projets http/https en local en passant par une url du type http://mon-projet.mon-domain.

Dans cette documentation, [[MY-DOMAIN]] doit être remplacé par le domain locale souhaité (comme localhost ou myname.kangourouge.com)

## Configuration

Il faut configurer Dnsmasq pour utiliser des domaines en local.


**Installation sur OSX avec brew:**

```bash
# install dnsmasq
brew install dnsmasq

# Create config folder if it doesn’t already exist
mkdir -pv $(brew --prefix)/etc/

# Configure dnsmasq for *.[[MY-DOMAIN]]
echo 'address=/.[[MY-DOMAIN]]/127.0.0.1' >> $(brew --prefix)/etc/dnsmasq.conf

# MacOS High Sierra ONLY!
echo 'port=53' >> $(brew --prefix)/etc/dnsmasq.conf

# Start dnsmasq as a service so it automatically starts at login
sudo brew services start dnsmasq

# Create a dns resolver
sudo mkdir -v /etc/resolver
sudo bash -c 'echo “nameserver 127.0.0.1” > /etc/resolver/[[MY-DOMAIN]]'
```

**Vérifier l'instalation:**

Avec Scutil

```bash
scutil --dns
```
Vous devriez voir ces lignes.
```bash
resolver #8

domain   : [[MY-DOMAIN]]

nameserver[0] : 127.0.0.1

flags    : Request A records, Request AAAA records

reach    : 0x00030002 (Reachable,Local Address,Directly Reachable Address)
```

Avec ping
```bash
ping foo.[[MY-DOMAIN]]
```
Donne
```bash
PING foo.[[MY-DOMAIN]] (127.0.0.1): 56 data bytes
64 bytes from 127.0.0.1: icmp_seq=0 ttl=64 time=0.027 ms
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.043 ms
...
```

## Installation

```bash
cp env.dist .env
# Remplacer le DOMAIN dans le fichier .env par votre domain set dans le dnsmasq
docker-compose up -d
```

## Usage

**Accéder au Dashbord de Traefik**

https://traefik.[[MY-DOMAIN]]/dashboard

**Avec docker-compose**

Dans votre docker-compose.yml
```yaml
services:
  mon-service-1:
    image: php-apache
    ports:
    - 80
    - 443
    labels:
    # Optionel si un seul port exposé
    - traefik.port=443
    # Determine l'url. Ici https://mon-service-php-apache.[[MY-DOMAIN]]
    - traefik.frontend.rule=Host:mon-service-php-apache.[[MY-DOMAIN]]
    # Ne pas oubier d'ajouter proxy dans les network!
    networks:
    - proxy

  mon-service-2:
    image: nodejs
    ports:
    - 3000
    labels:
    - traefik.frontend.rule=Host:mon-service-front.[[MY-DOMAIN]]
    networks:
    - proxy
    
    /.../
    
# Requis
networks:
  proxy:
    external:
      name: proxy
```

**Aller plus loin**

La [documentation](https://docs.traefik.io/v1.7) de Traefik

## Limites connues

**sql:**  
Traefik est un reverse proxy http/https. 
Il ne peut donc pas fournir d'addresse pour une base de donnée sql comme postgres par exemple.
