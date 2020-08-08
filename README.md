# docker-compose for simple ghost deployment

## usage

- create a `.env` file modeled after `.example.env`
- for email services to work, you have to have credentials for a mail service like Mailgun.
- `sudo docker-compose up -d`

## Nginx config

~~~
sudo touch /etc/nginx/sites-available/example-ghost-site
sudo nano /etc/nginx/sites-available/example-ghost-site
~~~

~~~
server {
  server_name example.com;
  listen [::]:80;
  listen 80;
  chunked_transfer_encoding on; # avoid HTTP 411 
  
  location / { 
    client_max_body_size 10G; # avoid HTTP 413 for large uploads
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-NginX-Proxy true;
    proxy_pass http://0.0.0.0:20001/;
    proxy_ssl_session_reuse off;
    proxy_set_header Host $http_host;
    proxy_cache_bypass $http_upgrade;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_read_timeout 900;
    proxy_redirect off;
  } 
}
~~~

~~~
sudo ln -s /etc/nginx/sites-available/example-ghost-site /etc/nginx/sites-enabled
~~~

You must set up SSL before you can connect!

## SSL

Just install and run `certbot`.


## acknowledgements
- https://ghost.org/docs/install/docker/
- https://autoize.com/docker-compose-file-ghost/
- https://sethbergman.tech/how-to-run-a-self-updating-ghost-blog-with-docker-compose/

