# Using actioncable & redis

### Install redis
```
https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-redis-on-ubuntu-16-04
```
### Config action cable

#### Config in app
```
config/environments/<env-name>.rb
config.action_cable.allowed_request_origins = [Rails.application.secrets.domain]
config.action_cable.disable_request_forgery_protection = true
```

```
$ mina ssh to=production
$ sudo vi /etc/nginx/conf.d/<domain>.conf
```

```
upstream grow_your_business {
  server unix:///home/growyour/production/shared/tmp/grow_your_business.sock;
}

server {

  listen   80;
  server_name     52.220.24.147;
  root '/home/growyour/production/current/public';

  client_max_body_size 256M;
  keepalive_timeout 120s;

  access_log /var/log/nginx/grow_your_business.access.log;
  error_log /var/log/nginx/grow_your_business.error.log warn;

  location /cable {
    proxy_pass http://grow_your_business;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "Upgrade";
  }
  # Rails asset pipeline support.
  location ~ "^/(assets|images|javascripts|stylesheets|system)/.+-([0-9a-f]{32}|[0-9a-f]{64})\..+" {
    access_log  off;
    gzip_static on;
    expires     max;
    add_header  Cache-Control public;
    add_header  Last-Modified "";
    add_header  ETag "";
    open_file_cache          max=1000 inactive=500s;
    open_file_cache_valid    600s;
    open_file_cache_errors   on;
    break;
  }

  try_files $uri/index.html $uri @grow_your_business;

  location @grow_your_business {
    proxy_connect_timeout   300s;
    proxy_send_timeout      600;
    proxy_read_timeout      600;
    proxy_buffer_size       64k;
    proxy_buffers        16 32k;
    proxy_temp_file_write_size  64k;
    proxy_pass_header    Set-Cookie;
    proxy_busy_buffers_size     64k;
    proxy_redirect      off;
    proxy_hide_header   Vary;
    proxy_set_header    Accept-Encoding '';
    proxy_ignore_headers Cache-Control Expires;
    proxy_set_header    Referer $http_referer;
    proxy_set_header    Host $host;
    proxy_set_header    Cookie $http_cookie;
    proxy_set_header    X-Real-IP $remote_addr;
    proxy_set_header    X-Forwarded-Host $host;
    proxy_set_header    X-Forwarded-Server $host;
    proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_pass          http://grow_your_business;
  }

  error_page 500 502 503 504 /500.html;

  location = /favicon.ico {
    expires    max;
    add_header Cache-Control public;
  }
}
```

```
sudo su -
$ service nginx stop
$ service nginx start
```