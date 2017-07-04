# dproxy

Dproxy permet de lancer en // sur de multiples projets utilisant docker.

## Installation

	git clone git@gitlab.smol.fr:smol/dproxy.git ~/Development/dproxy
	cd ~/Development/dproxy
	cp env.dist .env
	# remplacer le DOMAIN dans le fichier .env
	docker-compose up -d