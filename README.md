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

1.) Run `docker compose -f docker-compose.yml -f docker-compose.nfs.dev.yml up`.

2.) Refer to the applicable information in the section above.

### Running in production with a Prod NFS

TBD

###  Running Nextcloud OCC Commands

Maker sure you have everything running with `docker compose up -d` and then run `docker-compose exec --user www-data app php occ`.

----------------------------------------------------------------------------------------------------------
Made with ♥ in Los Angeles CA.