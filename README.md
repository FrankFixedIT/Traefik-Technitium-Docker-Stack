# Traefik-Technitium-Docker-Stack
Prerequisites (some may be optional, however, this is the way to guranteed success):
- Debian 12 (bare metal / kvm / lcx?)
- Docker installed: https://docs.docker.com/engine/install/debian/
- Cloudflare as Domain name registrar (I assume others would work as well)
- Cloudflare API token that has access to Zone Settings:Edit, Zone:Edit, DNS:Edit
- All commands assume root access, adjust accordingly.
1. Create folder structure and set permsissions. Change PASSWORD to a secure password for the Traefik portal, or your password will be PASSWORD!
```
apt update
apt install apache2-utils -y
mkdir docker
cd docker
touch docker-compose.yml
touch cf_api_token.txt
touch .env
chmod 600 .nev
chmod 600 cf_api_token.txt
mkdir traefik
cd traefik
touch acme.json
chmod 600 acme.json
touch traefik.yml
touch config.yml
docker network create proxy
echo $(htpasswd -nb "<USER>" "<PASSWORD>") | sed -e s/\\$/\\$\\$/g
```
2. Copy the value on the lasta line of the output. If you didn't change PASSWORD, your output should be
```
<USER>:$$apr1$$jFLLLiFS$$p4Lg.v3jiAwzu//15h7fn0
```
3. Move back to docker root folder
```
cd..
```
4. Edit .env file with your favorite editor, mine is nano
```
nano .env
#Past the below line, update the user to whatever you chose and change the hash with the value froom step 2
TRAEFIK_DASHBOARD_CREDENTIALS=user:$$apr1$$jFLLLiFS$$p4Lg.v3jiAwzu//15h7fn0
```
5. Update Cloudflare API key

```
nano cf_api_token.txt
#Past your Cloudflare API token
```
6. 
