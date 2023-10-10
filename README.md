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

1. Create a `docker-compose.site1.yml` file with a WordPress service definition, like:

```yaml
version: '3'

services:

  wordpress:
    image: wordpress:latest
    ports:
      - '8000:80'
    restart: always
    volumes: ['./site1/:/var/www/html']
    user: 1000:1000
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: root
      WORDPRESS_DB_PASSWORD: root
      WORDPRESS_DB_NAME: wordpress-1
    networks:
      - wpsites

networks:
  wpsites:
    external: true

```

2. Run `docker-compose -f docker-compose.site1.yml up -d` 

This will deploy a new WordPress container connected to the shared MySQL database.

3. Repeat to add additional sites.

## Deploying WordPress Without Docker Compose

This can also be deployed using `docker run` directly, without a Compose file:

```bash
docker run -d --name wordpress -p 8000:80 -v ./site1/:/var/www/html --user 1000:1000 -e WORDPRESS_DB_HOST=db -e WORDPRESS_DB_USER=root -e WORDPRESS_DB_PASSWORD=root -e WORDPRESS_DB_NAME=wordpress-1 --network wpsites --restart always wordpress:latest

```

This will create a WordPress container in the same network as the MySQL database.

This provides more flexibility to deploy WordPress without defining a full Compose file.