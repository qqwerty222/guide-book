
### $ means that next text is a variable
```
╭─bohdan@PF2FXPPG ~/sch
╰─$ echo $USER
bohdan
```

#### ' ' single quotes ignore all special characters
```
╭─bohdan@PF2FXPPG ~/sch
╰─$ echo '$USER / !@#$%^&*()_+'
$USER / !@#$%^&*()_+
```

#### " " double quotes don't ignore $ symbol 
```
╭─bohdan@PF2FXPPG ~/sch
╰─$ echo "user $USER"
user bohdan
```

#### \\ ignores next character

```
╭─bohdan@PF2FXPPG ~/sch
╰─$ echo "user $USER \$USER"
user bohdan $USER
```


