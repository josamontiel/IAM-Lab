# IAM-Lab
For my next trick, in an effort to increae my understanding of cybersecurity as a whole, I will be venturing into IAM territory. 

I will be using (mostly) free tools only, I already own the raspberry Pi, but the other tools will be free.

##### Tools
1. Linux (RaspberryPi OS, a debian flavor)
2. [Docker](https://docs.docker.com/get-started/get-docker/)
3. [OpenLDAP](https://www.openldap.org/)
4. [Keycloak](https://www.keycloak.org/)

The motivation behind using these tools is to focus on understanding IAM as a strategy and not just hyper-focusing on the tools (Entra, AWS etc). The use of Docker containers is to keep everything clean so I don't just have a bunch of stuff running on bare metal.

### Docker installation

To install Docker I ran the following commands:
```
## On Debian/Ubuntu
sudo apt update && sudo apt upgrade
sudo apt install curl vim git
```

Once installed, I ran the following commands to add my system user to the Docker group:
```
sudo usermod -aG docker $USER
newgrp docker
```
Once the user was added, I ran the following command to start and enable the service:

```
sudo systemctl start docker && sudo systemctl enable docker
```
Once enabled, the following command needs to be run to define a few desired variables.

```
docker run --name openldap-server \
	--env LDAP_ORGANISATION="My Company" \
	--env LDAP_DOMAIN="ldap.example.com" \
	--env LDAP_ADMIN_PASSWORD="StrongAdminPassw0rd" \
	--detach osixia/openldap:latest
```

Once that is done, its time to run OpenLDAP in the container:

```
docker run \
      --name openldap-server \
        -p 389:389 \
        -p 636:636 \
        --hostname ldap.computingforgeeks.com \
	--env LDAP_ORGANISATION="My Company" \
	--env LDAP_DOMAIN="computingforgeeks.com" \
	--env LDAP_ADMIN_PASSWORD="StrongAdminPassw0rd" \
        --env LDAP_BASE_DN="dc=computingforgeeks,dc=com" \
        --volume /data/slapd/database:/var/lib/ldap \
        --volume /data/slapd/config:/etc/ldap/slapd.d \
	--detach osixia/openldap:latest
```
After a few mins, you will be able to see your container running by running ```$docker ps``` as pictured below, the container is running

<img width="495" alt="Screenshot 2025-06-14 at 20 52 48" src="https://github.com/user-attachments/assets/b7fc3ded-cc32-46d6-9921-90bd046980fe" />

I also wanted web access incase I felt like I needed it so I am enabling PHPLDAPAdmin in Docker for a web UI:

```
docker run \
    --name phpldapadmin \
    -p 10080:80 \
    -p 10443:443 \
    --hostname phpldapadmin-service \
    --link openldap-server:ldap-host \
    --env PHPLDAPADMIN_LDAP_HOSTS=ldap.computingforgeeks.com \
    --detach osixia/phpldapadmin:latest
```
Once that is ready, we can now browse to the address and voila:
<img width="1384" alt="Screenshot 2025-06-14 at 21 04 13" src="https://github.com/user-attachments/assets/1a29d751-4fb3-4d8f-8061-61cce11e6848" />

Now we need to add users/groups and begin controling access...
