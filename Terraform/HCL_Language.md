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
	
- #### Resource Block
	- AWS
	```hcl
	resource "aws_instance" "web_server" { #BLOCK
		ami =           "ami-0347032742"  # Argument
		instance_type = var.instance_type # Argument with value as expression
	}
	```

- #### Input Variables Block
	- AWS
	```hcl
	variable "availability_zone" {
	    type    = string
	    default = null
	}

	variable "enable_dns_arec" {
	    type    = bool
		default = false
	}

	resource "aws_instance" "ec2" {
		...
		availability_zone = var.availability_zone
		...
	    private_dns_name_options {
	        enable_resource_name_dns_a_record = var.enable_dns_arec
	    }
	}
	```

- #### Local Variables Block
	```

	```

- #### Output Values Block
	```hcl
	# return list with private_ips
	output "private_ips" {
		value = var.private_ip
	}
	instance_ips = module.ec2.private_ips
	
	#return list with (Name: name, Pub_ip: 1.1.1, Pr_ip: 1.1.1) for each ec2
	output "ec2_ip" {
	    value = [
	        for x in aws_instance.ec2:
		    "Name: ${x.tags.Name} Public_ip: ${x.public_ip} ..."
	    ]
	}
	
	# output in parent module.[name_of_ec2].child_output
	output "ec2_ansible-master_ip" {
	    value = module.ansible-master.ec2_ip
	}

	# how it is looks like in terraform cli output:
	Outputs:
	ec2_ansible-master_ip = [
      (Name: ansible, Pub_ip: 1.1.1.1, Pr_ip: 1.1.1.1),
    ]
	ec2_dns_ip            = [
      (Name: dns, Pub_ip: 1.1.1.2, Pr_ip: 1.1.1.2),
    ]
	ec2_user_ip           = [
      (Name: user, Pub_ip: 1.1.1.3, Pr_ip: 1.1.1.3),
      (Name: user2, Pub_ip: 1.1.1.4, Pr_ip: 1.1.1.4),
    ]
   
	```

- #### Modules Block
	```hcl
	module "dns" {
		# path to resource folder (must have)
	    source = "../modules/ec2"

	    # all of the vars is created with null value in resource folder
	    list = ["dns"]
	    instance_type     = "t2.micro"
	    ami               = "ami-03e08697c325f02ab"
	    
	    availability_zone = var.availability_zone
	    subnet_id         = module.dns_net.subnet_id
	    private_ip        = ["10.0.1.15"]
    
	    # from output ../modules/aws_key_pair/outputs|key_name
	    key_name          = module.dns_key_pair.key_name
	    sec_group_ids     = [module.icmp.sec_group_id]
	}
	```

***




