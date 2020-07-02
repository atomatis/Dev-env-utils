# DNSmasqu

Rappel: [[MY-DOMAIN]] doit être remplacé par votre domaine

Dnsmasqu permet de faire pointer (en local seulement) des adresses du style xxxx.mon_domain_local vers localhost par exemple.

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
```
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
```
PING foo.[[MY-DOMAIN]] (127.0.0.1): 56 data bytes
64 bytes from 127.0.0.1: icmp_seq=0 ttl=64 time=0.027 ms
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.043 ms
...
```
