***
## Demo site
```
/sites/demo1
├── index.html
├── style.css
└── thumb.png
```

***
## Set custom header

```bash
        server {

                listen 80;
                server_name 10.0.2.11;

                location /hi {
                        return 200 "hello from Nginx";
                        add_header my_header "Test header";
                }

        }
```

```
bohdan@nginx:~$ curl localhost/hi
hello from Nginx
bohdan@nginx:~$ curl -I localhost/hi
HTTP/1.1 200 OK
Server: nginx/1.18.0 (Ubuntu)
Date: Fri, 17 Feb 2023 12:35:57 GMT
Content-Type: text/plain
Content-Length: 16
Connection: keep-alive

my_header: Test header
```

***
## Set up cache

```
        server {
                listen 80;
                server_name 10.0.2.11;
                root /sites/demo1;
                
                location = /thumb.png {
                        add_header Cache-Control public;
                        add_header Pragma public;
                        add_header Vary Accept-Encoding;
                        expires 15m;
                }
        }
```

```
bohdan@nginx:~$ curl -I localhost/thumb.png
HTTP/1.1 200 OK
Server: nginx/1.18.0 (Ubuntu)
Date: Fri, 17 Feb 2023 12:44:18 GMT
Content-Type: image/png
Content-Length: 12627
Last-Modified: Thu, 16 Feb 2023 11:30:32 GMT
Connection: keep-alive
ETag: "63ee13d8-3153"
Expires: Fri, 17 Feb 2023 12:59:18 GMT
Cache-Control: max-age=900
Cache-Control: public
Pragma: public
Vary: Accept-Encoding
Accept-Ranges: bytes
```

