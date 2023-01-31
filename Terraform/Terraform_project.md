Deploy DNS/Nginx/Host docker containers, and configure them with ansible. 

***
## Configuration
- Windows 10
- (WSL) Ubuntu server 22.04

***
## Define main terraform file

#### Set up provider configuration
- There we set settings to work with docker.
- First resource will be ubuntu 
```
# main.tf

terraform {
	required_providers {
		docker = {
			source = "kreuzwerker/docker"
			version = "~> 2.13.0"
		}
	}
}

provider "docker" {}

resource "docker_image" "ubuntu" {
  name = "ubuntu:latest"
  keep_locally = false
}
```

#### Set up first resource
```
resource "docker_container" "test" {
  image = docker_image.ubuntu.latest
  name = "ubuntu-test"
  restart = "always"
  stdin_open = true
  tty = true
  ports {
  	internal = 22
  	external = 22
  }
  
  provisioner "local-exec" {
    command = "docker exec ubuntu-test bash -c ' apt-get update ' "
  }
  provisioner "local-exec" {
    command = "docker exec ubuntu-test bash -c ' apt-get install openssh-server python3 -y '"
  
}
```

```
  provisioner "local-exec" {
    command = "docker exec ubuntu-test bash -c ' apt-get update ' "
  }
  provisioner "local-exec" {
    command = "docker exec ubuntu-test bash -c ' apt-get install openssh-server python3 -y '"
  }
  provisioner "local-exec" {
    command = "docker exec ubuntu-test bash -c ' service ssh start && service ssh enable '"
  }
  provisioner "local-exec" {
    command = "docker exec ubuntu-test bash -c ' echo "root:pass" | chpasswd' "
  }
  provisioner "local-exec" {
    command = "docker exec ubuntu-test bash -c 'sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config' "
  }
  provisioner "local-exec" {
    command = "docker exec ubuntu-test bash -c 'sed "s\@session\s\*required\s\*pam_loginuid.so\@session optional pam_loginuid.so\@g" -i /etc/pam.d/ssh' "
  }
```