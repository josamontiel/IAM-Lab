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
version: '3.8'

services:
  openldap:
    image: osixia/openldap:1.5.0
    container_name: openldap
    environment:
      - LDAP_ORGANISATION=IAM-Lab
      - LDAP_DOMAIN=iam-lab.com
      - LDAP_ADMIN_PASSWORD=<yeah nice try>
    ports:
      - "389:389"
      - "636:636"
    volumes:
      - ldap_data:/var/lib/ldap
      - ldap_config:/etc/ldap/slapd.d

  phpldapadmin:
    image: osixia/phpldapadmin:0.9.0
    container_name: phpldapadmin
    environment:
      - PHPLDAPADMIN_LDAP_HOSTS=openldap
      - PHPLDAPADMIN_HTTPS=true
    ports:
      - "8443:443"
    depends_on:
      - openldap

volumes:
  ldap_data:
  ldap_config:

```
Once that is ready, we can now browse to the address and voila:
<img width="1384" alt="Screenshot 2025-06-14 at 21 04 13" src="https://github.com/user-attachments/assets/1a29d751-4fb3-4d8f-8061-61cce11e6848" />

Now we need to add users/groups and begin controling access...

To create new users and groups, I followed the following [article](https://computingforgeeks.com/run-openldap-server-in-docker-containers/). Once created it's time to configure KeyCloak. To download KeyCloak we run the following command to set it up in a container:

```
docker run -p 8080:8080 -e KC_BOOTSTRAP_ADMIN_USERNAME=admin -e KC_BOOTSTRAP_ADMIN_PASSWORD=<nice try> quay.io/keycloak/keycloak:26.2.5 start-dev
```
Keycloak is an open-source identity and access management tool that provides single sign-on, user authentication, and authorization for applications. It supports modern protocols like OAuth2, OpenID Connect, and SAML, and allows integration with LDAP, Active Directory, and external identity providers like Google or GitHub.
