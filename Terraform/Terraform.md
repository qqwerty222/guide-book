***
# What is?

Terraform is Infrastructure as a code tool, it can automate your deployment process by using human-readable files with code that describes infrastructure configuration.

Also Terraform supported by a lot of Cloud Providers and infrastructure platfrom such as Docker.

***
# Example deploy using Terraform

## Configuration
- Windows 10
- (WSL) Ubuntu server 22.04

## Install Terraform

#### Install gnupg, gpg and software-properties-common
- This packages will be used to verify signatures of packages
	- must Hashicorp it is an owner of Terraform software
```
╭─bohdan@PF2FXPPG ~
╰─$ sudo apt-get install gpg gnupg software-properties-common
```

#### Download the signing key
```
╭─bohdan@PF2FXPPG ~
╰─$ wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg
```

#### Verify signature
```
╭─bohdan@PF2FXPPG ~
╰─$ gpg --no-default-keyring --keyring /usr/share/keyrings/hashicorp-archive-keyring.gpg --fingerprint
```

#### Add HashiCorp repo
```
╭─bohdan@PF2FXPPG ~
╰─$ echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
```

#### Install Terraform
```
╭─bohdan@PF2FXPPG ~
╰─$ sudo apt update
...
╭─bohdan@PF2FXPPG ~
╰─$ sudo apt install terraform
```

***
## Create terraform file

#### Define first block ( terraform { } )
- This block include setting of providers that will be provision your infrastructure
	- source attribute defines an optional hostname, namespace and the provider type.
		- you can find available providers there [Terraform Registry](https://registry.terraform.io/)
	- also you can specify specific version.
```
terraform {
	required_providers {
		docker = {
			source = "kreuzwerker/docker"
			verion = "~> 2.13.0"
		}
	}
}
```

#### Define provider { } 
- This block set Terraform plugin that will be used to interact with provider
```
provider "docker" {}
```

#### Define resource {}
- This block describe components of your infrastructure
	- On the first row you see resource type (docker_image) and resource name (nginx)
	- Next arguments you use to configure the resource it can be machine size, disk image, ports...
	- 
```
resource "docker_image" "nginx" {
	name 		 = "nginx:latest"
	keep_locally = false
}

resource "docker_container" "nginx" {
	image = docker_image.nginx.latest
	name = "nginx"
	ports {
		internal = 80 
		external = 8000
	}
}
```

#### Final file
```
terraform {
	required_providers {
		docker = {
			source = "kreuzwerker/docker"
			version = "~> 2.13.0"
		}
	}
}

provider "docker" {}

resource "docker_image" "nginx" {
	name 				 = "nginx:latest"
	keep_locally = false
}

resource "docker_container" "nginx" {
	image = docker_image.nginx.latest
	name = "nginx"
	ports {
		internal = 80 
		external = 8000
	}
}
```

***
## Initialize terraform file

#### Load configuration from file 
```
╭─bohdan@PF2FXPPG ~/docker/terraform
╰─$ ls
main.tf  
---------------------------------------------------------------------------------╭─bohdan@PF2FXPPG ~/docker/terraform
╰─$ terraform init
Initializing the backend...

Initializing provider plugins...
- Reusing previous version of kreuzwerker/docker from the dependency lock file
- Using previously-installed kreuzwerker/docker v2.13.0
...
```

#### Check changes
- There you can see services that will be started 
```
╭─bohdan@PF2FXPPG ~/docker/terraform
╰─$ terraform plan

Terraform used the selected providers to generate the following execution plan. Resource
actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # docker_container.nginx will be created
  + resource "docker_container" "nginx" {
      + attach           = false
      + bridge           = (known after apply)
      + command          = (known after apply)
      + container_logs   = (known after apply)
      + entrypoint       = (known after apply)
      + env              = (known after apply)
      + exit_code        = (known after apply)
      + gateway          = (known after apply)
      + hostname         = (known after apply)
      + id               = (known after apply)
      + image            = (known after apply)
...
```

#### Deploy configuration
```
╭─bohdan@PF2FXPPG ~/docker/terraform
╰─$ terraform apply
...
Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
```

#### Destroy configuration
```
╭─bohdan@PF2FXPPG ~/docker/terraform
╰─$ terraform destroy
...
Destroy complete! Resources: 2 destroyed.
```

***
## Inspect current configuration

#### Show current configuration
To store confiration Terraform uses file with .tfstate extension. So it must be saved in secure.
Also to get current configuration you can use next command
```
---------------------------------------------------------------------------------╭─bohdan@PF2FXPPG ~/docker/terraform
╰─$ terraform show
# docker_container.nginx:
resource "docker_container" "nginx" {
    attach            = false
    command           = [
        "nginx",
        "-g",
        "daemon off;",
    ]
    cpu_shares        = 0
    entrypoint        = [
        "/docker-entrypoint.sh",
    ]
...
```

#### Manage terraform state

```
╭─bohdan@PF2FXPPG ~/docker/terraform
╰─$ terraform state
...
Subcommands:
    list                List resources in the state
    mv                  Move an item in the state
    pull                Pull current state and output to stdout
    push                Update remote state from a local state file
    replace-provider    Replace provider in the state
    rm                  Remove instances from the state
    show                Show a resource in the state
```

```
╭─bohdan@PF2FXPPG ~/docker/terraform
╰─$ terraform state list                                                                
docker_container.nginx
docker_image.nginx
```

```
╭─bohdan@PF2FXPPG ~/docker/terraform
╰─$ terraform state show docker_container.nginx
# docker_container.nginx:
resource "docker_container" "nginx" {
    attach            = false
    command           = [
        "nginx",
        "-g",
        "daemon off;",
    ]
    cpu_shares        = 0
    entrypoint        = [
        "/docker-entrypoint.sh",
    ]
```

***
## Variables in terraform files 

####  Create file .tf file
```
╭─bohdan@PF2FXPPG ~/docker/terraform
╰─$ touch variables.tf
```

#### Define variable
```
variable "internal_port" {
		description = "Number of port will opened internal"
		type 		= string
		default     = 45
}
```

#### Use variable in main terraform file
```
...
	ports {
		internal = var.internal_port 
		external = 8000
	}
...
```

#### Apply changes in configuration
```
╭─bohdan@PF2FXPPG ~/docker/terraform
╰─$ terraform apply
...
      ~ ports {
          ~ internal = 80 -> 45 # forces replacement
            # (3 unchanged attributes hidden)
        }
...
```

***
## Set up outputs in terraform 

Outputs is variables that can store data from provider api

#### Create file for outputs
```
╭─bohdan@PF2FXPPG ~/docker/terraform
╰─$ touch outputs.tf
```

#### Define outputs
```
output "container_id" {
	description = "ID of the Docker container"
	value 		= docker_container.nginx.id
}

output "image_id" {
	description = "ID of the Docker image"
	value 		= docker_image.nginx.id
}
```

#### Apply changes and show outputs
```
╭─bohdan@PF2FXPPG ~/docker/terraform
╰─$ terraform apply
...
Changes to Outputs:
  + container_id = "a61072911cdc5032c4348a1c0e96d1ccc4ff9edb5d474fa962c5d3310d54d0a0"
  + image_id     = "sha256:a99a39d070bfd1cb60fe65c45dea3a33764dc00a9546bf8dc46cb5a11b1b50e9nginx:latest"
...
╭─bohdan@PF2FXPPG ~/docker/terraform
╰─$ terraform output
container_id = "a61072911cdc5032c4348a1c0e96d1ccc4ff9edb5d474fa962c5d3310d54d0a0"
image_id = "sha256:a99a39d070bfd1cb60fe65c45dea3a33764dc00a9546bf8dc46cb5a11b1b50e9nginx:latest"
```

***
