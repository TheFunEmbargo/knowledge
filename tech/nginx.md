# nginx

Its a high performance webserver. A dedicated front door. Below is configured as a reverse proxy to make available locally hosted bits on the 'net. You can have as many .confs as services you require. Load balance configuration is also possible!

```app.conf

# listen to https
server {
    listen 443 ssl; 
    listen [::]:443 ssl;

    server_name $DOMAIN_NAME;

    # timeout settings
    proxy_connect_timeout   6000;
    proxy_send_timeout      6000;
    proxy_read_timeout      6000;
    send_timeout            6000;

    # reverse proxy to localhost:3000
    # this will proxy https requests to this VMs IP or the server name to this ip
    # set headers fowards ... headers
    location / {
        proxy_pass http://127.0.0.1:3000/;
        proxy_set_header X-Forwarded-For	$proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto	$scheme;
        proxy_set_header X-Forwarded-Host	$host;
        proxy_set_header X-Forwarded-Prefix	/;
    }

    # domain ssl configuration
    # these are the 
    ssl_certificate /etc/letsencrypt/live/$DOMAIN_NAME/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/$DOMAIN_NAME/privkey.pem;

    # default ssl settings
    # [pass ssl labs security tests](https://www.ssllabs.com/index.html)
    include /etc/letsencrypt/options-ssl-nginx.conf;
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;
}


# listen to http 
server {
    listen 80;
    server_name $DOMAIN_NAME; 
    server_tokens off;

    # serve the SSL certificates on acme challenge
    location '/.well-known/acme-challenge/' {
        default_type "text/plain";
        root /var/www/certbot;
    }

    # redirect all other traffic to HTTPS
    location / {
        return 301 https://$host$request_uri;
    }
}

```

The main nginx `/etc/nginx/nginx.conf` will load all of these in (see `include /etc/nginx/conf.d/*.conf;`)


# cerbot

Manage SSL

0. Get certbot `sudo apt install certbot`
1. Obtain certs `sudo certbot --nginx -d [domain]` creates certs in folder `/etc/letsencrypt/live/[domain]/`
2. Renew certs `sudo certbot renew` [every 12 hrs](https://eff-certbot.readthedocs.io/en/stable/using.html#setting-up-automated-renewal)

_Note: 5 a week request limit per domain & its subdomains. Use `--dry-run` to test._

There is a catch 22 whereby nginx needs Certbot to verify the domain and generate SSL certificates, but if options-ssl-nginx.conf (see `default ssl configuration` above) is missing, Nginx will fail to start.

1. Remove or comment out the SSL-related lines in the above Nginx configuration, ensuring to listen to 80, or use:
```
server {
    listen 80;
    server_name $DOMAIN_NAME; 

    location /.well-known/acme-challenge/ {
        root /var/www/certbot;
    }
}
```
1. issue the certs
1. comment in / reinstate the ssl lines & 443 listener
1. reload nginx `nginx -s reload` (no downtime!)

# dockerising this

[docker-nginx-certbot image](https://github.com/JonasAlfredsson/docker-nginx-certbot/tree/master)

nginx should be treated as a global configuration for the vm.


```nginx-certbot.yml
services:
  # nginx container serves all envrionments
  # handles SSL certificates with certbot, using configuration files in ./build/nginx/conf
  # https://github.com/JonasAlfredsson/docker-nginx-certbot
  nginx-cerbot:
    container_name: nginx-cerbot
    image: jonasal/nginx-certbot:latest
    restart: unless-stopped
    env_file:
      - .env.build
    ports:
      - 80:80
      - 443:443
    # these will be shadowed if run from multiple dirs !!
    # mount to user_conf.d which is symlinked to conf.d, enabling the config magic this image does
    # https://github.com/JonasAlfredsson/docker-nginx-certbot/blob/master/docs/good_to_know.md#the-user_confd-folder
    # not certaint this is strictly required as we handle the redirector logic above in the app.conf see `Redirect all other traffic to HTTPS`
    volumes:
      - ./build/nginx/conf/:/etc/nginx/user_conf.d/:ro
      - ./build/certbot/www/:/var/www/certbot/:ro
```

