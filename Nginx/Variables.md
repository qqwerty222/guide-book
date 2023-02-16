[Alphabetical index of variables (nginx.org)](https://nginx.org/en/docs/varindex.html)
***
## Add variables in return block

```bash
        ### Use variables
                location /inspect {
                        return 200 "$host \n$uri";
                }
        }
```

```bash
bohdan@nginx:~$ curl localhost/inspect
localhost
/inspect
```

***
## Get variable arguments

```bash
bohdan@nginx:~$ curl localhost/inspect?hello=nginx
localhost
/inspect
hello=nginx
```

```bash
        # Get variable arguments
                location /inspect {
                        return 200 "Name: $arg_name";
                }
        }
```

```bash
bohdan@nginx:~$ curl localhost/inspect/?name=Alex
Name: Alex
```

***
## Set variable arguments and use if statement

```bash
        server {
				...
                set $key '12345';

                if ( $arg_key != $key ) {
                        return 401 "Incorrect API Key";
                }
                
            # Check key
                location /key {
                        return 200 "Key is correct";
                }
        }       
```

```bash
bohdan@nginx:~$ curl localhost/key
Incorrect API Key
bohdan@nginx:~$ curl localhost/key?key=12345
Key is correct
```
