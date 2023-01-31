***
# What is?

HashiCorp Configuration Language. Used by Terraform and designed to be both machine and human-readable. HCL is built using cod econfiguratoin blocks which typically follow next syntax:

***
## HCL Syntax:

```hcl
# Template

<BLOCK TYPE> "<BLOCK LABEL>" "<BLOCK LABEL>" {
	# Block body
	<IDENTIFIER> = <EXPRESSION> # Argument
}

# AWS EC2 Example
resource "aws_instance" "web_server" { #BLOCK
	ami =           "ami-0347032742"  # Argument
	instance_type = var.instance_type # Argument with value as expression
}
```

***
## Block types:

- #### Settings Block
	- Docker
	```hcl
	terraform {
	  required_providers {
	    docker = {
	      source = "kreuzwerker/docker"
	      version = "3.0.1"
	    }
	  }
	}
	```

- #### Provider Block
	- AWS
	```hcl
	provider "aws" {
		access_key = "some_acccess_key"
		secret_key = "some_secret_key"
		region = "us-east-1"
	}
	```

	- Docker
	```hcl
	provider "docker" {}
	```

- #### Resource Block
	- AWS
	```hcl
	resource "aws_instance" "web_server" { #BLOCK
		ami =           "ami-0347032742"  # Argument
		instance_type = var.instance_type # Argument with value as expression
	}
	```

	- Docker
	```hcl
	resource "docker_image" "ssh-ready" {
	  name = "ssh-ready"
	  keep_locally = false
	  force_remove = true
	
	  build {
	    context = "."
	    tag     = ["ssh-ready:v1"]
	  }
	}
	```

- #### Data Block
	```

	```

- #### Input Variables Block
	```

	```

- #### Local Variables Block
	```

	```

- #### Output Values Block
	```

	```

- #### Modules Block
	```

	```

***




