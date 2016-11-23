# Docker for Multi-Web Digital Ocean Server

### Includes
- nginx proxy
- letsencrypt volumne
- letsencrypt add domain script
- letsencrypt cronjob

### Adding domains
Adding a new domain to let's encrypt via the docker container can be done with the `~/docker/letsencrypt/add_domain` script. The first argument must be the *domain* to be added and the second your *email*.

```
~/docker/letsencrypt/add_domain yourdomain.com you@email.com
```

The `add_domain` script runs the following code:

```
docker run -it --rm --name letsencrypt \
  -v "/var/lib/letsencrypt:/var/lib/letsencrypt" \
  --volumes-from nginx \
  quay.io/letsencrypt/letsencrypt \
  certonly \
  --webroot \
  --webroot-path /var/www/html \
  --agree-tos \
  --renew-by-default \
  -d yourdomain.com \
  -m you@email.com
```

### Renewal

**Testing renewal**

```
docker run -it --rm --name letsencrypt \
  -v "/var/lib/letsencrypt:/var/lib/letsencrypt" \
  --volumes-from nginx \
  quay.io/letsencrypt/letsencrypt \
  renew \
  --text \
  --dry-run
```

**Actual renewal**

```
docker run -it --rm --name letsencrypt \
  -v "/var/lib/letsencrypt:/var/lib/letsencrypt" \
  --volumes-from nginx \
  quay.io/letsencrypt/letsencrypt \
  renew \
  --text 
```

This should be done by `letsencrypt/cron_letsencrypt`. To activate it symlink it to `/etc/cron.daily/cron_letsencrypt`.

```
ln -s ~/docker/letsencrypt/cron_letsencrypt /etc/cron.daily/cron_letsencrypt
```