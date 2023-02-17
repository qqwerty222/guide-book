***
## Demo site
```
/sites/demo3
└── access.log
```

***
## Set access log path for location
```bash
        server {

                listen 80;
                server_name 10.0.2.11;
                
                #default
                access_log /var/log/nginx/access.log;
                error_log  /var/log/nginx/error.log;

                location /test {
                        access_log /sites/demo3/access.log;
                        return 200 "Some text";
                }
        }
```

```bash
bohdan@nginx:~$ curl localhost/test
bohdan@nginx:~$ tail -f /sites/demo3/access.log
127.0.0.1 - - [17/Feb/2023:12:03:13 +0000] "GET /test HTTP/1.1" 200 9 "-" "curl/7.81.0"
```

***
## Default log format 
```bash
log_format default     '$remote_addr - $remote_user [$time_local] '
					   '"$request" $status $body_bytes_sent '
					   '"$http_referer" "$http_user_agent";

#access.log
10.0.2.10 - - [15/Feb/2023:15:31:28 +0000] "GET /HTTP/1.1" 200 336 "-" "curl/7.81.0" 
```

***
## Create custom log format to display upstream ip
- some unused parameters were deleted, $upstream_addr added
```bash
log_format upstream     '[$time_local] $remote_addr $upstream_addr '  
						'"$request" $status $body_bytes_sent '
						'"$http_user_agent"';

#access.log
[17/Feb/2023:15:27:03 +0000] 10.0.2.10 172.1.1.10:8000 "GET / HTTP/1.1" 200 336 "curl/7.81.0"
```

***
## Set custom log format (for upstream conf)
- log format must be in http block
- to use specify it name after access_log block
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

