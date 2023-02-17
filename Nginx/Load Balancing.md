***
## Create upstream

```bash
http {
    upstream websites {
        server 172.1.1.10:8000;
        server 172.1.1.11:8000;
        server 172.1.1.12:8000;
    }

    log_format upstream     '[$time_local] $remote_addr $upstream_addr '  
                            '"$request" $status $body_bytes_sent '
                            '"$http_user_agent"';
    server{
        listen      80;
        server_name localhost;
        
        access_log /var/log/nginx/access.log upstream;
        error_log  /var/log/nginx/error.log;

        location / {
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header X-Forwarded-Proto $scheme;
                proxy_set_header Host $http_host;

                proxy_pass http://websites;
        }
    }
}
```

```bash
#access.log
[17/Feb/2023:15:51:35 +0000] 10.0.2.10 172.1.1.10:8000 "GET / HTTP/1.1" 200 336 "curl/7.81.0"
[17/Feb/2023:15:51:35 +0000] 10.0.2.10 172.1.1.11:8000 "GET / HTTP/1.1" 200 336 "curl/7.81.0"
[17/Feb/2023:15:51:35 +0000] 10.0.2.10 172.1.1.12:8000 "GET / HTTP/1.1" 200 336 "curl/7.81.0"
[17/Feb/2023:15:51:35 +0000] 10.0.2.10 172.1.1.10:8000 "GET / HTTP/1.1" 200 336 "curl/7.81.0"
[17/Feb/2023:15:51:36 +0000] 10.0.2.10 172.1.1.11:8000 "GET / HTTP/1.1" 200 336 "curl/7.81.0"

```