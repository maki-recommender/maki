# Maki

**This version of Maki is currently under development and should not be used in production**

[Maki](https://makichan.xyz/) is an open source anime recommendation REST API that suggests anime to watch based on the user lists on [Anilist](https://anilist.co/) or [MyAnimeList](https://myanimelist.net/).

## Getting Started

Maki is made by multiple repositories:
- [maki-web](https://github.com/rickycorte/maki-web): web app using Maki API
- [maki-api](https://github.com/maki-recommender/maki-api): Maki API server handling user requests
- [maki-model](https://github.com/maki-recommender/maki-model): Maki recommendation model 
- [mak-data-updater](https://github.com/maki-recommender/maki-data-updater): service that updates anime information

### Development

To run a development copy of Maki use:

`docker compose -f docker-compose.dev.yml up -d`

this will run all the containers with ports exposed on the host machine and default usernames and passwords. This compose file will not run nginx, database backup, and periodic model retrain.

**NOTE:** do not use this compose file in production!

### Deployment

Before you start you will need a domain with DNS access, a server and TLS/SSL certificates to enable HTTPS. If you plan to periodically run automated retrains be aware that a lot of RAM will be required for this operation especially if you have a large database of user lists and anime. You can also deploy Maki behind [Cloudflare](https://www.cloudflare.com/) and use their Let's Encrypt Certificates. 

If you already have the certificate and its key you can place them in the `cert/` folder and name them `certificate.pem` and `private.key`. If you need a different setup feel free to edit `nginx/nginx.conf` file. If you don't want to deploy Nginx as reverse proxy you can remove it from the `docker-compose.yml` file.

You will also need an account of an object storage provides with S3-like API to run periodic database backups. While Maki does not store mission-critical data, it is annoying and slow to collect again all the user lists and anime details from scratch.

Before running the compose file you may want to create a `.env` file to define the following environment variables:

```
PG_dbName=...
PG_Username=...
PG_Password=...
S3_Endpoint=...
S3_KeyId=...
S3_Bucket=...
```

To further customize Maki's behavior you can change the env variables of each container. For your convenience all variables are already listed in the compose file with their default value. For more information about env variables visit the repositories of each service.

Once done, if you want to use Nginx, open `nginx/nginx.conf` and replace `api.makichan.xyz` with the domain where you will run Maki.

Once done you can run `docker compose up -d` to start everything.