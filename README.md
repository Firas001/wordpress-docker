# Docker Compose for Multi WordPress Sites

This repository provides a Docker Compose configuration for running multiple WordPress sites sharing a common MySQL database.

## Docker Compose Services

The following services are configured:

- `db` - MySQL database 
- `phpmyadmin` - PhpMyAdmin for database access and administration

## Getting Started 

To start services:

```
docker-compose up -d
```

This will pull the needed Docker images, and start the containers in the background.

PhpMyAdmin can be accessed at: `http://localhost:8080`

Default credentials are:

- Username: root
- Password: root

## Deploying WordPress With Docker Compose

To add a new WordPress site:

1. Create new directory to store your WordPress files, e.g. wpsite1
2. Create new database for your wordpress, e.g. wpsite1
3. Find your local UID (User ID) and GID (Group ID) using following bash command: `id -u && id -g`
4. Create `docker-compose.wpsite1.yml` file with the following WordPress service definition:

```yaml
version: '3'

services:

  wpsite1:
    image: wordpress:latest
    ports:
      - '8000:80'
    restart: always
    volumes: ['./wpsite1/:/var/www/html']
    user: 1000:1000
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: root
      WORDPRESS_DB_PASSWORD: root
      WORDPRESS_DB_NAME: wpsite1
    networks:
      - wpsites

networks:
  wpsites:
    external: true

```

This will deploy a new WordPress container connected to the shared MySQL database.

5. Repeat to add additional sites.

## Deploying WordPress Without Docker Compose

This can also be deployed using `docker run` directly, without a Compose file:

```bash
docker run -d --name wpsite1 -p 8000:80 -v ./wpsite1/:/var/www/html --user 1000:1000 -e WORDPRESS_DB_HOST=db -e WORDPRESS_DB_USER=root -e WORDPRESS_DB_PASSWORD=root -e WORDPRESS_DB_NAME=wpsite1 --network wpsites --restart always wordpress:latest

```

This provides more flexibility to deploy WordPress without defining a full Compose file.


## Before running, be sure to customize:

* The container name with your desired name instead of `wpsite1`
* The host volume mount directory instead of `./wpsite1`
* The MySQL database name instead of `wpsite1`
* The `user: 1000:1000` line with your local UID/GID. 