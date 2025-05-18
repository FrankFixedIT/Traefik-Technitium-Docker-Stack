# Traefik-Technitium-Docker-Stack
Prerequisites (some may be optional, however, this is the way to guranteed success):
- Debian 12 (bare metal / kvm / lcx?)
- Docker installed: https://docs.docker.com/engine/install/debian/
- Cloudflare as Domain name registrar (I assume others would work as well, google accordingly)
- Cloudflare API token that has access to Zone Settings:Edit, Zone:Edit, DNS:Edit
- Proper Domain DNS entries on Cloudflare #To verify you are allowed to do all of this on the domain you are doing it on
- All commands assume root access, adjust accordingly.
1. Create folder structure and set permsissions. Change PASSWORD to a secure password for the Traefik portal, or your password will be PASSWORD
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
2. Copy the value on the last line of the output. If you didn't change PASSWORD, your output should be
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
5. Add Cloudflare API token
```
nano cf_api_token.txt
#Past your Cloudflare API token
```
6. Next we need to edit 3 files: docker-compose.yml, traefik/config.yml and treafik/traefik.yml. Copy the contents of each file from this repo. Any line that starts with a # needs to be adjusted.
```
nano docker-compose.yml
nano treafik/config.yml
nano treafik/traefik.yml
``` 
7. Bring up the stack and cross our fingers
```
docker compose up -d
``` 
8. Your stack should be available at the follwing addesses (providedd you have create the proper DNS entries whever you are currrently hosting DNS):
```
#Technitium portal:
https://technitium.HOSTNAME.YourDomain.TLD
Note, if this is your first DNS server access via:
http://IP:5380
```
9. Once you have access to the Technitium portal, the follwing changes must be made to activate DNS-over-TLS and DNS-over-HTTPS
- Navigate to Settings - Optional Procotolls
- Enable 'Enable DNS-over-TCP-PROXY' and 'Enable DNS-over-HTTP'
- Set 'DNS-over-TCP-PROXY Port' to 538
- Set 'DNS-over-HTTP Port' to 8081
- Add 'Reverse Proxy Network ACL'
```
10.0.0.0/8
172.16.0.0/12
192.168.0.0./16
127.0.0.0/8
```
- Save settings
10. Now DNS-over-TLS and DNS-over-HTTPS should be available for clients.
- #DNS-overHTTPS Quieres as available at
- https://dns.HOSTNAME.YourDomain.TLD
- DONS-over-TLS port is available at
- HOSTANME.YourDomain.TLD:853
