# dproxy

Dproxy permet de trvailler en // sur de multiples projets utilisant docker.

## Installation

	git clone git@gitlab.smol.fr:smol/dproxy.git ~/Development/dproxy
	cd ~/Development/dproxy
	docker-compose up -d

Il expose sur le port `80` tous les containers ayant un port publié et la variable d'env `VIRTUAL_HOST` définie. Lorsque plusieurs ports sont publiés, il faut préciser le port à utiliser via la variable `VIRTUAL_PORT`. Cette publication pouvant être implicite, il est recommandé de toujours ajouter `VIRTUAL_PORT`.

exemple d'application avec docker-compose: 

	mysql:
	  image: percona
	  volumes:
	    - /mnt/sda1/lms/mysql:/var/lib/mysql
	  environment:
	    - MYSQL_ROOT_PASSWORD=rpass
	    - MYSQL_DATABASE=drupal
	    - MYSQL_USER=drupal
	    - MYSQL_PASSWORD=pass
	  ports:
	    - "3306"
	
	mailcatcher:
	  build: ./mailcatcher
	  environment:
	   - VIRTUAL_HOST=mc.lms.docker
	   - VIRTUAL_PORT=1080
	  ports:
	    - "1080"
	
	drupal:
	  build: ./drupal
	  volumes:
	    - /data/lms/sites:/var/www/html/sites
	  links:
	    - mysql
	    - mailcatcher
	  environment:
	   - VIRTUAL_HOST=lms.docker
	   - VIRTUAL_PORT=80
	  ports:
	    - "80"


Dans cet exemple, 2 services sont définis, qu'il faut également ajouter au fichier hosts de la machine (l'ip peut varier suivant les installations):

	10.211.55.3 lms.docker
	10.211.55.3 mc.lms.docker

Le port mysql n'a pas été configuré, car VIRTUAL_HOST n'est pas défini. dproxy ne prend en charge pour le moment que le http.

Pour accéder au mysql, il faut trouver le port de cette façon :

	docker-compose port mysql 3306