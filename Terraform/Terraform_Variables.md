***
## Template

```hcl
	variable "<VARIABLE_NAME>" {
		# Block body
		type        = <VARIABLE_TYPE | string | ...>
		descritpion = <DESCRIPTION>
		default     = <EXPRESSION>
		sensitive   = <BOOLEAN> 
		validation  = <RULES>
	}
```

### AWS Example
```hcl
variable "aws_region" {
	type = string
	description = "region used to deploy infrastructure"
	default = "eu-central-1"
	validation {
		condition = can(regex("^eu-", var.aws_region))
		error_message = "The aws_region value not valid"
	}
}
```



####  Create .tf file
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
