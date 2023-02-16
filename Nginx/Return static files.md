## Demo site
```
/sites/demo1
├── index.html
├── style.css
└── thumb.png
```

#### Add http server block
- define http server block
- specify port to listen (80 is default for http)
- server_name can be dns name or ip address of current machine
- root is folder from where take files answered from user
```bash
#/etc/nginx/nginx.conf
events {}

http {
        server {

                listen 80;
                server_name 10.0.2.11;

                root /sites/demo1;
        }
}
```

#### Reload configuration
- nginx -t is check syntax of nginx.conf
- reload means that if some trouble will happen while restart, nginx will continue working on previous configuration.
```bash
bohdan@nginx:~$ sudo nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful

bohdan@nginx:~$ sudo systemctl reload nginx
```

#### Request some file from nginx
- index.html is default, you don't need to specify it
```bash
bohdan@nginx:~$ curl localhost/index.html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>NGINX Fundamentals</title>
    <link rel="stylesheet" href="style.css" type="text/css">
</head>
<body>
    <header>
        <div class="inner">
            <img class="thumb" src="thumb.png" alt="NGINX Logo">
            <div class="heading">
                <h1>NGINX Fundamentals</h1>
...
```

```bash
bohdan@nginx:~$ curl localhost
<!DOCTYPE html>
<html>

<head>
    <meta charset="UTF-8">
    <title>NGINX Fundamentals</title>
    <link rel="stylesheet" href="style.css" type="text/css">
</head>

<body>

    <header>
        <div class="inner">
            <img class="thumb" src="thumb.png" alt="NGINX Logo">
            <div class="heading">
                <h1>NGINX Fundamentals</h1>
```

- Request style.css for example
```bash
bohdan@nginx:~$ curl localhost/style.css
html,
body {
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Oxygen,
    Ubuntu, Cantarell, "Open Sans", "Helvetica Neue", sans-serif;
  margin: 0 auto;
  padding: 0;
}

header {
  color: white;
  background: #073a6f;
  padding: 2rem 1rem;
  margin-bottom: 2rem;
  overflow: auto;
}
...
```

***
## Configure file types

#### By default nginx returns all files as a plain text
```bash
bohdan@nginx:~$ curl -I localhost/style.css
HTTP/1.1 200 OK
Server: nginx/1.18.0 (Ubuntu)
Date: Thu, 16 Feb 2023 11:55:49 GMT

Content-Type: text/plain

Content-Length: 980
Last-Modified: Thu, 16 Feb 2023 11:30:32 GMT
Connection: keep-alive
ETag: "63ee13d8-3d4"
Accept-Ranges: bytes
```

#### Add file types
```bash
http {
        types {
                text/html html;
                text/css  css;
        }

        server {
                listen 80;
                server_name 10.0.2.11;
                root /sites/demo1;
        }
}
```

#### Request css file again
```bash
bohdan@nginx:~$ curl -I localhost/style.css
HTTP/1.1 200 OK
Server: nginx/1.18.0 (Ubuntu)
Date: Thu, 16 Feb 2023 12:02:07 GMT

Content-Type: text/css

Content-Length: 980
Last-Modified: Thu, 16 Feb 2023 11:30:32 GMT
Connection: keep-alive
ETag: "63ee13d8-3d4"
Accept-Ranges: bytes
```

#### mime.types file
- by default nginx already has the file with all most commonly used file types
```bash
bohdan@nginx:~$ cat /etc/nginx/mime.types

types {
    text/html                             html htm shtml;
    text/css                              css;
    text/xml                              xml;
    image/gif                             gif;
    image/jpeg                            jpeg jpg;
    application/javascript                js;
    application/atom+xml                  atom;
    application/rss+xml                   rss;
    ...
```

#### Include mime.types file
```bash
http {
        include mime.types;

        server {
                listen 80;
                server_name 10.0.2.11;
                root /sites/demo1;
        }
}
```

```bash
bohdan@nginx:~$ curl -I localhost/style.css
HTTP/1.1 200 OK
Server: nginx/1.18.0 (Ubuntu)
Date: Thu, 16 Feb 2023 12:12:30 GMT

Content-Type: text/css

Content-Length: 980
Last-Modified: Thu, 16 Feb 2023 11:30:32 GMT
Connection: keep-alive
ETag: "63ee13d8-3d4"
Accept-Ranges: bytes
```