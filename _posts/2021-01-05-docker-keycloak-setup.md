---
title: "Docker Keycloak setup"
date: 2019-04-18T15:34:30-04:00
categories:
  - blog
tags:
  - devops
  - keycloak
  - docker
---

Steps to setup keycloak in docker:
1. Install Docker
2. Intall Docker Compose

In your server execute the below commands

3. Create a directory called docker-keycloak
`mkdir docker-keycloak`

4. Cd into docker-keycloak
`cd docker-keycloak`

5. Create a file called `docker-compose.yml` with the following contents

`vi docker-compose.yml`

```yaml
version: '3.7'

services:
  postgres:
      image: postgres:12.2
      container_name: postgres
      environment:
        POSTGRES_DB: keycloak
        POSTGRES_USER: keycloak
        POSTGRES_PASSWORD: password
      volumes:
        - ./postgres-data:/var/lib/postgresql/data

  keycloak:
      image: jboss/keycloak:9.0.2
      container_name: keycloak
      environment:
        DB_VENDOR: POSTGRES
        DB_ADDR: postgres
        DB_DATABASE: keycloak
        DB_USER: keycloak
        DB_PASSWORD: password
        KEYCLOAK_USER: admin
        KEYCLOAK_PASSWORD: password
        PROXY_ADDRESS_FORWARDING: "true"
      ports:
        - 9995:8080
      depends_on:
        - postgres

  mailhog:
    image: mailhog/mailhog:latest
    container_name: mailhog
    ports:
      - "8025:8025"
```

`:wq`

6. Execute the docker-compose file created

`docker-compose up -d`

7. Access keycloak's admin console @ your_ip:9995

8. Setup your admin email to test email recieval
a. click the top right corner which has the text "admin"
b. click manage account
c. add in your email and save.

8. Email setup can be tricky, for testing purposes use the below method

goto `your_ip:9995/auth/admin/master/console/#/realms/master/smtp-settings`

Set Host : aspmx.l.google.com
Set Port : 25
Set From : your-gmail@gmail.com
Set Enable StartTLS : True
Set EnableAuthentication : True
Username: your-gmail@gmail.com
Password: password-of-your-gmail-id

Rest can be set to blank

9. Setup email to allow less secure access

a. Goto this link - https://myaccount.google.com/lesssecureapps
b. Click enable

10. go back to `your_ip:9995/auth/admin/master/console/#/realms/master/smtp-settings` and click test connection
you should recieve an email in the junk folder of the email you set for admin.
