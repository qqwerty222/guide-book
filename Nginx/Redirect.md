***
## Demo site
```
/sites/demo1
├── index.html
├── style.css
└── thumb.png
```

***
## Use return
```bash
        server {
                listen 80;
                server_name 10.0.2.11;
                root /sites/demo1;
                
                location /logo {
                        return 307 /thumb.png;
                }
        }
```

```bash
bohdan@nginx:~$ curl -I localhost/logo
HTTP/1.1 307 Temporary Redirect
Server: nginx/1.18.0 (Ubuntu)
Date: Thu, 16 Feb 2023 13:49:09 GMT
Content-Type: text/html
Content-Length: 180
Location: http://localhost/thumb.png
Connection: keep-alive

bohdan@nginx:~$ curl localhost/logo
<html>
<head><title>307 Temporary Redirect</title></head>
<body>
<center><h1>307 Temporary Redirect</h1></center>
<hr><center>nginx/1.18.0 (Ubuntu)</center>
</body>
</html>
```

## Use rewrite ( redirect internally, client won't know)
- Redirect from page "user/(any word)" on page /hi
```bash
			server {
				...

                rewrite ^/user/\w+ /hi;

			    ...

                location /hi {
                        return 200 "Hello from nginx";
                }
			}
```

```bash
bohdan@nginx:~$ curl localhost/user/john
Hello from nginx

bohdan@nginx:~$ curl -I localhost/user/john
HTTP/1.1 200 OK
Server: nginx/1.18.0 (Ubuntu)
Date: Thu, 16 Feb 2023 14:11:14 GMT
Content-Type: text/plain
Content-Length: 16
Connection: keep-alive
```

#### Use variables in rewrite block
```bash
        server {

			    rewrite ^/user/(\w+) /hi/$1;

                location /hi {
                        return 200 "Hello from nginx";
                }

                location /hi/ben {
                        return 200 "Hello ben";
                }
        }
```

```bash
bohdan@nginx:~$ curl localhost/user/john
Hello from nginx

bohdan@nginx:~$ curl localhost/user/ben
Hello ben
```

#### Combine rewrite
```bash
        server {
                rewrite ^/user/(\w+) /hi/$1;
                rewrite ^/hi/ben   /thumb.png;

                location /hi {
                        return 200 "Hello from nginx";
                }

                location /hi/ben {
                        return 200 "Hello ben";
                }

        }
```

```
bohdan@nginx:~$ curl -I localhost/hi/ben
HTTP/1.1 200 OK
Server: nginx/1.18.0 (Ubuntu)
Date: Thu, 16 Feb 2023 14:36:21 GMT

Content-Type: image/png

Content-Length: 12627
Last-Modified: Thu, 16 Feb 2023 11:30:32 GMT
Connection: keep-alive
ETag: "63ee13d8-3153"
Accept-Ranges: bytes
```