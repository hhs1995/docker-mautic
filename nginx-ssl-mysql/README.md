Configuring **Nginx** to act as a reverse proxy with SSL support for **Mautic** container.

## 1. Configuring in `docker-compose.yml`:

* [x] Setup of **web_marketing <-> mautic** & **database_marketing <-> mysql** containers
* [x] Setup of a **nginx_marketing <--> nginx** container with custom vhost configuration `nginx.conf`
* [x] Automatic creation of self-signed certificate

## 2. Installing:

1. Run $ ```docker-compose up``` or `docker-compose up -d` in this directory
2. Add this line to your `/etc/hosts` file ```127.0.4.123 marketing.local``` or [MacOS] ```127.0.0.1 marketing.local```
3. Access https://marketing.local
4. Add the presented certificate to a trusted certificates list in your browser (the certificate is a self-signed certificate created on the first run of this example)
5. Go through Mautic setup (fill ```MysqlRootPassword``` as Mysql password on db setup page
6. Test Mautic

## Configuring

- After install

```
  ## login to the docker: 
  docker exec -it {dockerid} bash

  ## clear the cache: 
  php app/console cache:clear

  ## set the user ownership to www-data again: 
  ## chmod -R 775 /var/www/html
  chown -R www-data:www-data /var/www/html/
```

#### Notes
* The certificate should be made trustworthy. It may not be enough to just click trough the warning in some browsers or $ `curl https://marketing.local --insecure`.
* The hosts mapping is needed to make the certificate trustworthy (name must match with certificate's CN).
* If you want to access the container remote machine you should replace `127.0.4.123` or `127.0.0.1` with address of your docker host.
* `docker-compose down -v`