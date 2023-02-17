***
## Set up proxy
Proxy_pass means that your url will be redirected to another address
```bash
        server {
                listen 80;
                server_name 10.0.2.15;

                location / {
					proxy_pass http://172.1.1.10:8000;
                }
        }
```

```bash
bohdan@nginx:~$ curl 10.0.2.15
<!doctype html>
<title>Posts - Flask</title>
<link rel="stylesheet" href="/static/style.css">
<nav>
  <h1>Test_1</h1>

bohdan@nginx:~$ curl -I 10.0.2.15
HTTP/1.1 200 OK
Server: nginx/1.22.1
Date: Fri, 17 Feb 2023 13:47:32 GMT
Content-Type: text/html; charset=utf-8
Content-Length: 336
Connection: keep-alive

# nginx/access.log
10.0.2.11 - - [17/Feb/2023:13:47:32 +0000] "HEAD / HTTP/1.1" 200 0 "-" "curl/7.81.0"
```

## Use "curl -x" to check nginx proxy
- curl -x (nginx address:port) (website address:port)
```bash
bohdan@nginx:~$ curl -x 10.0.2.15:80 172.1.1.10:8000
<!doctype html>
<title>Posts - Flask</title>
<link rel="stylesheet" href="/static/style.css">
<nav>
  <h1>Test_1</h1>

bohdan@nginx:~$ curl -I -x 10.0.2.15:80 172.1.1.10:8000
HTTP/1.1 200 OK
Server: nginx/1.22.1
Date: Fri, 17 Feb 2023 13:48:44 GMT
Content-Type: text/html; charset=utf-8
Content-Length: 336
Connection: keep-alive

# nginx/access.log
10.0.2.11 - - [17/Feb/2023:13:48:44 +0000] "HEAD http://172.1.1.10:8000/ HTTP/1.1" 200 0 "-" "curl/7.81.0"
```

***
## Set headers only for client
```bash
                location / {
	                add_header proxied nginx;
					proxy_pass http://172.1.1.10:8000;
                }
```

```bash
bohdan@nginx:~$ curl -I 10.0.2.15
HTTP/1.1 200 OK
Server: nginx/1.22.1
Date: Fri, 17 Feb 2023 13:54:30 GMT
Content-Type: text/html; charset=utf-8
Content-Length: 336
Connection: keep-alive
proxied: nginx
```

***
## Set header for target website (on proxy request)
```bash
                location / {
	                proxy_set_header proxied nginx;
					proxy_pass http://172.1.1.10:8000;
                }
```

```bash
bohdan@nginx:~$ curl -I 10.0.2.15
HTTP/1.1 200 OK
Server: nginx/1.22.1
Date: Fri, 17 Feb 2023 13:55:24 GMT
Content-Type: text/html; charset=utf-8
Content-Length: 336
Connection: keep-alive
```