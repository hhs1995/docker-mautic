[Docker Image] Mautic Digital Marketing Automation on Amazon AWS
===================
<img src="README/docker-mautic.png" />

[![Build Status](https://travis-ci.org/vbosstech/docker-mautic.svg)](https://travis-ci.org/vbosstech/docker-mautic)

## How to use this image

You can access and customize Docker Mautic from [Official Docker Hub image](https://hub.docker.com/u/vbosstech/mautic/).

* [x] Setup Automated Builds using [GitHub](https://github.com/vbosstech/docker-mautic) and [Docker Hub](https://hub.docker.com/u/vbosstech)
* 


## Pulling image from Docker Hub

If you want yo pull the latest stable image from DockerHub:

	# docker pull mautic/mautic:latest
	docker pull mautic/mautic:fpm

There are also another images that fit your needs:

| Tag         | PHP    | Web Server | Compatibility |
|-------------|:------:|:----------:|:---------------:|
| fpm         | 7.1.23 | Nginx      | <= 2.15.0 |
| latest      | 7.1.23 | Apache     | <= 2.15.0 |
| apache      | 7.1.23 | Apache     | <= 2.15.0 |
|             |        |            | >= 2.15.0 |
| beta-apache | 7.2.12 | Apache     | >= 2.15.0 |
| beta-fpm    | 7.2.12 | Nginx      | >= 2.15.0 |

*You can use the beta images to test latest beta releases of Mautic with current PHP version.*

## Running Basic Container

Setting up MySQL Server:

	$ docker volume create mysql_data

	$ docker run --name percona -d \
         -p 3306:3306 \
         -e MYSQL_ROOT_PASSWORD=Job4U \
         -v mysql_data:/var/lib/mysql \
         percona/percona-server:5.7 \
         --character-set-server=utf8 --collation-server=utf8_general_ci

Running Mautic:

	$ docker volume create marketing_data

	$ docker run --name mautic -d \
        --restart=always \
        -e MAUTIC_DB_HOST=127.0.0.1 \
        -e MAUTIC_DB_USER=root \
        -e MAUTIC_DB_PASSWORD=Job4U \
        -e MAUTIC_DB_NAME=mautic \
        -e MAUTIC_RUN_CRON_JOBS=true \
        -e MAUTIC_TRUSTED_PROXIES=0.0.0.0/0 \
        -p 8080:80 \
        -v marketing_data:/var/www/html \
        mautic/mautic:latest

This will run a basic mysql service within Mautic on http://localhost:8080.

## Customizing Mautic Container

The following environment variables are also honored for configuring your Mautic instance:

#### Database Options
-	`-e MAUTIC_DB_HOST=...` (defaults to the IP and port of the linked `mysql` container)
-	`-e MAUTIC_DB_USER=...` (defaults to "root")
-	`-e MAUTIC_DB_PASSWORD=...` (defaults to the value of the `MYSQL_ROOT_PASSWORD` environment variable from the linked `mysql` container)
-	`-e MAUTIC_DB_NAME=...` (defaults to "mautic")
-	`-e MAUTIC_DB_TABLE_PREFIX=...` (defaults to empty) Add prefix do Mautic Tables. Very useful when migrate existing databases from another server to docker.

If you'd like to use an external database instead of a linked `mysql` container, specify the hostname and port with `MAUTIC_DB_HOST` along with the password in `MAUTIC_DB_PASSWORD` and the username in `MAUTIC_DB_USER` (if it is something other than `root`).


If the `MAUTIC_DB_NAME` specified does not already exist on the given MySQL server, it will be created automatically upon startup of the `mautic` container, provided that the `MAUTIC_DB_USER` specified has the necessary permissions to create it.

### Mautic Options
-	`-e MAUTIC_RUN_CRON_JOBS=...` (defaults to true - enabled) If set to true runs mautic cron jobs using included cron daemon
-	`-e MAUTIC_TRUSTED_PROXIES=...` (defaults to empty) If it's Mautic behing a reverse proxy you can set a list of comma separated CIDR network addresses it sets those addreses as trusted proxies. You can use `0.0.0.0/0` or See [documentation](http://symfony.com/doc/current/request/load_balancer_reverse_proxy.html)
-	`-e MAUTIC_CRON_HUBSPOT=...` (defaults to empty) Enables mautic crons for Hubspot CRM integration
-	`-e MAUTIC_CRON_SALESFORCE=...` (defaults to empty) Enables mautic crons for Salesforce integration
-	`-e MAUTIC_CRON_PIPEDRIVE=...` (defaults to empty) Enables mautic crons for Pipedrive CRM integration
-	`-e MAUTIC_CRON_ZOHO=...` (defaults to empty) Enables mautic crons for Zoho CRM integration
-	`-e MAUTIC_CRON_SUGARCRM=...` (defaults to empty) Enables mautic crons for SugarCRM integration
-	`-e MAUTIC_CRON_DYNAMICS=...` (defaults to empty) Enables mautic crons for Dynamics CRM integration

### Enable / Disable Features
-	`-e MAUTIC_TESTER=...` (defaults to empty) Enables Mautic Github Pull Tester  [documentation](https://github.com/mautic/mautic-tester)

### PHP options
-	`-e PHP_INI_DATE_TIMEZONE=...` (defaults to `UTC`) Set PHP timezone
-	`-e PHP_MEMORY_LIMIT=...` (defaults to `256M`) Set PHP memory limit
-	`-e PHP_MAX_UPLOAD=...` (defaults to `20M`) Set PHP upload max file size
-	`-e PHP_MAX_EXECUTION_TIME=...` (defaults to `300`) Set PHP max execution time

### Persistent Data Volumes

On first run Mautic is unpacked at `/var/www/html`. You need to attach a volume on this path to persist data.

### Mautic Versioning

Mautic Docker has two ENV that you can specify an version do start your new container:

 - `-e MAUTIC_VERSION` (Defaults to "2.15.0")
 - `-e MAUTIC_SHA1` (Defalts to "b07bd42bb092cc96785d2541b33700b55f74ece7")

## Accesing the Instance

Access your new Mautic on `http://localhost:8080` or `http://host-ip:8080` in a browser.


## ... via [`docker-compose`](https://github.com/docker/compose)

Example `docker-compose.yml` for `mautic`:

```yml
version: "2"
services:
  mautic:
    image: mautic/mautic:latest
    container_name: mautic
    depends_on:
      - mauticdb
    ports:
      - 8080:80
    volumes:
      - mautic_data:/var/www/html
    environment:
      - MAUTIC_DB_HOST=mauticdb
      - MAUTIC_DB_USER=root
      - MAUTIC_DB_PASSWORD=mysqlrootpassword
	    - MAUTIC_DB_NAME=mautic

  mauticdb:
    image: mysql:5.6
    container_name: mauticdb
    ports:
      - 3306:3306
    volumes:
      - mautic_db:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=mysqlrootpassword

volumes:
  - mautic_data:
  - mautic_db:			
```

Run `docker-compose up`, wait for it to initialize completely, and visit `http://localhost:8080` or `http://host-ip:8080`.

# Supported Docker versions

This image is officially supported on Docker version 1.7.1.

Support for older versions (down to 1.0) is provided on a best-effort basis.