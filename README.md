# nexus

```
docker pull sonatype/nexus3
docker run -d -p 8081:8081 --name nexus sonatype/nexus3

```

## Access via https -> nginx

### Generate the certificat
* https://github.com/davidboukari/ssl/
```
mkdir -p /etc/nginx/<domainname>
cp private.{crt,key} /etc/nginx/<domainname> 

cat nginx-mydomain.com.conf
    server {
        listen       443 ssl http2 default_server;
        listen       [::]:443 ssl http2 default_server;
        server_name  _;
        root         /usr/share/nginx/html;

        ssl_certificate  /etc/nginx/<domainname>/private.crt;
        ssl_certificate_key  /etc/nginx/<domainname>/private.key;
        ssl_session_cache shared:SSL:1m;
        ssl_session_timeout  10m;
        ssl_ciphers HIGH:!aNULL:!MD5;
        ssl_prefer_server_ciphers on;

        # Proxy settings
        proxy_set_header X-Real-IP $remote_addr;
        proxy_ssl_session_reuse on;
        location / {
          proxy_set_header   X-Forwarded-Host $http_host;
         # proxy_set_header   Host $host:$server_port;
          proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
          proxy_set_header   X-Forwarded-Proto $scheme;
          proxy_http_version 1.1;
          proxy_set_header X-Real-IP $remote_addr;
          proxy_set_header Host $host;

          proxy_pass         http://127.0.0.1:8081;
          proxy_redirect     http://127.0.0.1:8081   https://<CHANGETHEIP>:4459;
        }

systemctl restart nginx

# Go to 
https://<CHANGETHEIP>:4456
```

## Get the admin password
```
cp nexus:/nexus-data/admin.password ./nexus-credenial.txt
```

=> login: amdin / passwd

## Deploy python app to nexus
* https://www.nova-technology.fr/2019/03/27/repository-python/

