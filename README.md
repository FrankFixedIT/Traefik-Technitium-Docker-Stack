# Traefik-Technitium-Docker-Stack
Prerequisites (some may be optional, however, this is the way to guranteed success):
- Debian 12 (bare metal / kvm / lxc?)
- Docker installed: https://docs.docker.com/engine/install/debian/
- Cloudflare as Domain name registrar (I assume others would work as well, google accordingly)
- Cloudflare API token that has access to Zone Settings:Edit, Zone:Edit, DNS:Edit
- Proper A record on Cloudflare #Let's Encrypt needs to verify you are allowed to do all of this on the domain that you are doing it on (no ports need to opened on the firewall)
- All commands assume root user access, adjust accordingly.
1. Install apache2-utils for password hash generation. Create folder structure, files and set permsissions. Create docker network.
- IMPORTANT: Change PASSWORD to a secure password for the Traefik portal, or your password will be PASSWORD
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
cd..
docker network create proxy
echo $(htpasswd -nb "<USER>" "<PASSWORD>") | sed -e s/\\$/\\$\\$/g
```
2. Copy the value on the last line of the output. If you didn't change PASSWORD, your output should be
```
<USER>:$$apr1$$jFLLLiFS$$p4Lg.v3jiAwzu//15h7fn0
```
- Also, if you didn't change PASSWORD, try the las line of the command in step 1 again, but be better.
3. Edit .env file with your favorite editor, mine is nano (<a href=https://linuxize.com/post/how-to-use-nano-text-editor>How to use nano</a>)
```
nano .env
#Paste the below line, update the user to whatever you chose and change the hash (everything right of : ) with the value froom step 2
TRAEFIK_DASHBOARD_CREDENTIALS=user:$$apr1$$jFLLLiFS$$p4Lg.v3jiAwzu//15h7fn0
```
4. Add Cloudflare API token
```
nano cf_api_token.txt
#Paste your Cloudflare API token
```
5. Next we need to edit 3 files: docker-compose.yml, traefik/config.yml and treafik/traefik.yml. Copy the contents of each file from this repo. Any line that starts with a # needs to be adjusted and have the # removed. Any line that starts with ## is a hint for what follows.
```
nano docker-compose.yml
nano treafik/config.yml
nano treafik/traefik.yml
``` 
6. Bring up the stack and cross our fingers
```
docker compose up -d
```
- Use the below command to verify everything is up
```
docker ps -a
```
- Use the below command for basic troubleshooting
 ```
docker compose logs
```
7. Your Technitium portal should be available at the follwing addesses (providedd you have created the proper A/CNAME DNS entries whever you are currrently hosting DNS):
- https://technitium.HOSTNAME.YourDomain.TLD
- Note, if this is your first DNS server, access via:
- http://IP:5380
8. Once you have access to the Technitium portal, the follwing changes must be made to activate DNS-over-TLS and DNS-over-HTTPS
- Navigate to Settings - Optional Procotolls
- Enable 'Enable DNS-over-TCP-PROXY' and 'Enable DNS-over-HTTP'
- Set 'DNS-over-TCP-PROXY Port' to 538
- Set 'DNS-over-HTTP Port' to 8081
- Add 'Reverse Proxy Network ACL'
```
#Adjust as needed
10.0.0.0/8
172.16.0.0/12
192.168.0.0./16
127.0.0.0/8
```
- Save settings
9. Now DNS-overUDP/TCP, DNS-over-TLS and DNS-over-HTTPS are be available for clients.
- DNS-over-HTTPS is available at
- https://dns.HOSTNAME.YourDomain.TLD/dns-query
- DNS-over-TLS is available at
- HOSTANME.YourDomain.TLD:853
  0. The Traefik portal is available at
- https://traefik.HOSTNAME.YourDomain.TLD
  1. Best practice
- Once access to the Technitium portal via FQDN is available (A/CNAME records in your newly creataed zone on Technitium (I hope, or why are even doing this?)), change line 53 in docker-compose.yml to remove direct access via IP. (<a href=https://github.com/FrankFixedIT/IPTalbesSetupDebian>IPTables</a> are cool too.)
```
      - "5380/tcp"
``` 
- Commit the change
```
docker compose down && docker compose up -d
``` 
