# What is
Define actions to do when user request some path 
***
## Demo site
```
/sites/demo2
└── loc
    └── in_loc
```

***
## Add location /loc
```bash
events {}

http {
        include mime.types;
        server {
                listen 80;
                server_name 10.0.2.11;
                root /sites/demo2;

                location /loc {
                        return 200 'Hello from nginx /loc';
                }
        }
}
```

```bash
bohdan@nginx:~$ curl localhost/loc
Hello from nginx /loc

bohdan@nginx:~$ curl -I localhost/loc
HTTP/1.1 200 OK

Server: nginx/1.18.0 (Ubuntu)
Date: Thu, 16 Feb 2023 12:25:52 GMT
Content-Type: text/plain
Content-Length: 21
Connection: keep-alive
```

***
## Prefix match location

```bash
        # Prefix match
                location /loc {
                        return 200 'Hello from nginx /loc';
                }
```

```bash
bohdan@nginx:~$ curl localhost/loc
Hello from nginx /loc

bohdan@nginx:~$ curl localhost/loc/in_loc
Hello from nginx /loc
```

## Prioritized prefix location

```bash
        # Prefix match
                location /loc {
                        return 200 'Hello from nginx /loc';
                }

        # Priority prefix match
                location ^* /loc {
                        return 200 'Hello from nginx /loc PRIORTIZED';
                }
```

```
bohdan@nginx:~$ curl localhost/loc
Hello from nginx /loc PRIORTIZED
```
## Exact match location

```bash
bohdan@nginx:~$ curl localhost/loc
Hello from nginx /loc

bohdan@nginx:~$ curl localhost/loc/in_loc

```

## Regex match location
```bash
        #Regex match
                # add "location ~*" to make it case sensitive
                location ~ /loc[0-9] {
                        return 200 'Hello from nginx /loc*';
                }
        }
```

```bash
bohdan@nginx:~$ curl localhost/loc1
Hello from nginx /loc*

bohdan@nginx:~$ curl localhost/loc5
Hello from nginx /loc*

bohdan@nginx:~$ curl localhost/loc3
Hello from nginx /loc*
```