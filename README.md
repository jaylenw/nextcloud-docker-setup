# My Nextcloud Docker Setup

**Goal**: Support a Docker Compose environment for running Nextcloud with MariaDB, Redis, and supports using an NFS for Nextcloud's `data` directory. 

This project was inpired and references [Nextcloud Docker](https://github.com/nextcloud/docker).

## System Requirements

**Docker Engine**: ^20.10.18

**Docker Compose**: ^2.10.2

You will also need an NFS server configured to support two environments, one for development testing and another for production use if you choose to use an NFS as
your  data storage for Nextcloud.

## Getting Started

1.) Make sure you have [Docker Engine](https://www.docker.com/) and [Docker Compose](https://docs.docker.com/compose/) installed.

2.) Clone this repository.

3.) Copy both the `.db-env.example` and `.nextcloud-env.example` files to have `.db-env` and `.nextcloud-env` files in your directory. Proceed to set the appropriate values in each of the files.

4.) Copy both the `docker-compose.nfs.dev.yml.example` and `docker-compose.nfs.prod.yml.example` files to have `docker-compose.nfs.dev.yml` and `docker-compose.nfs.prod.yml` files in your directory.

### Local Testing

To get started with this, it is best that you are able to get Nextcloud running locally first before deploying it to your deployment environment (ex. your server).
We will first get Nextcloud working without NFS to make sure all is well.

1.) Run `docker compose pull`.

2.) Run `docker compose up` and after all of the services initialization, open your browser to `http://localhost:8080/` and proceed to install Nextcloud.

3.) After Nextcloud is installed you can stop the services with Ctrl+C. Uncomment `NEXTCLOUD_INIT_HTACCESS` to enable the updating of the `.htacess` file(s).

4.) Restart the services with `docker compose up` and login with the admin user you created during installation.

Congrats, we've successfully got Nextcloud running with Docker Compose.

### Local Testing with a Dev NFS

**Caution:** When modifying the Docker volumes settings with Docker Compose, you may have to run `docker compose down -v` for the new
settings to take place. This will delete EVERYTHING in your volumes if you have something in there (ie. database, Nextcloud data). Please be
careful running the command, make sure you know what you are doing, and take backups!

1.) Make the necessary changes to the `docker-compose.nfs.dev.yml` file for `device` and `o` under `driver_opts`.

2.) Run `docker compose -f docker-compose.yml -f docker-compose.nfs.dev.yml up`.

3.) Refer to the applicable information in the section above.

### Running in production with a Prod NFS

1.) Run `docker compose -f docker-compose.yml -f docker-compose.nfs.prod.yml up` after making applicable changes to the `docker-compose.nfs.prod.yml` file.

2.) Configure NGINX to forward traffic to your Nextcloud application. I strongly suggest having a proxy server in front of your Nextcloud docker environment.

Below is an example of a reverse-proxy configuration to get you started using [NGNIX](https://www.nginx.com/).
*PLEASE CONFIGURE YOUR DEPLOYMENT WITH SSL. You can do so with [Certbot](https://certbot.eff.org/).*

```
# configuration for nextcloud production environment
upstream nextcloud-prod-backend {
    # the nextcloud application in nextcloud container
    server <localhost or whatever IP>:<port>; # ip and use the port specified in the docker-compose.yml
    keepalive 64;
}

server {
    # the virtual host name of this
    listen 80;
    server_name <your-domain-or-subdomain>;

    location / {
        proxy_pass http://nextcloud-prod-backend;
        proxy_set_header X-Forwarded-Host $host;
        proxy_set_header X-Forwarded-Server $host;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $host;
        # set max upload size, should be the same size as PHP_UPLOAD_LIMIT
        client_max_body_size 512M;
    }
}
```
3.) Access your application in the browser and check that all is well.

###  Running Nextcloud OCC Commands

Maker sure you have everything running with `docker compose up -d` and then run `docker-compose exec --user www-data app php occ`.

----------------------------------------------------------------------------------------------------------
Made with ♥ in Los Angeles CA.