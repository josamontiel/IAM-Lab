# IAM-Lab
For my next trick, in an effort to increae my understanding of cybersecurity as a whole, I will be venturing into IAM territory. 

I will be using (mostly) free tools only, I already own the raspberry Pi, but the other tools will be free.

##### Tools
1. Linux (RaspberryPi OS, a debian flavor)
2. [Docker](https://docs.docker.com/get-started/get-docker/)
3. [Keycloak](https://www.keycloak.org/)

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
Once that in enabled we can look to installing KeyCloak.

```
docker run -p 8080:8080 -e KC_BOOTSTRAP_ADMIN_USERNAME=admin -e KC_BOOTSTRAP_ADMIN_PASSWORD=<nice try> quay.io/keycloak/keycloak:26.2.5 start-dev
```
Keycloak is an open-source identity and access management tool that provides single sign-on, user authentication, and authorization for applications. It supports modern protocols like OAuth2, OpenID Connect, and SAML, and allows integration with LDAP, Active Directory, and external identity providers like Google or GitHub.

<img width="1361" alt="Screenshot 2025-06-15 at 22 18 29" src="https://github.com/user-attachments/assets/046903b3-4620-42c7-825e-124a3946849f" />

