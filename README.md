# Traefik-Technitium-Docker-Stack
#Change PASWWORD to a secure password for the Traefik portal.
```
apt update
apt install apache2-utils -y
mkdir docker
cd docker
touch docker-compose.yml
touch cf_api_token.txt
touch .env
chmod 600 .nev
chmod 600 cf_afi_token.txt
mkdir traefik
cd traefik
touch acme.json
chmod 600 acme.json
touch traefik.yml
touch config.yml
docker network create proxy
#echo $(htpasswd -nb "<USER>" "<PASSWORD>") | sed -e s/\\$/\\$\\$/g
```

