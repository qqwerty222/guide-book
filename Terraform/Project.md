Deploy DNS/Nginx/Host docker containers, and configure them with ansible. 

***
## Configuration
- Windows 10
- (WSL) Ubuntu server 22.04
- AWS

## Task for the project
- Create vpc and subnet
- Create security groups and ssh_keys
- Create instance as ansible master
- Create 3 instances to be provisioned by ansible master
- Fill hosts and variables in ansible playbook automatically

https://github.com/qqwerty222/terraform-project

***
## Structure

```
├── cloud-init_scripts
│   └── ansible_master.yml
├── live
│   ├── config.tf
│   ├── data.tf
│   ├── outputs.tf
│   ├── project-ec2.tf
│   ├── project-network.tf
│   ├── project-s_groups.tf
│   ├── project-ssh_keys.tf
│   ├── secret.auto.tfvars
│   ├── secret.auto.tfvars_reference
│   ├── terraform.tfstate
│   ├── terraform.tfstate.backup
│   └── variables.tf
├── modules
│   ├── aws_key_pair
│   │   ├── main.tf
│   │   ├── outputs.tf
│   │   └── variables.tf
│   ├── ec2
│   │   ├── main.tf
│   │   ├── outputs.tf
│   │   └── variables.tf
│   ├── network
│   │   ├── main.tf
│   │   ├── outputs.tf
│   │   └── variables.tf
│   └── security_groups
│       ├── main.tf
│       ├── outputs.tf
│       └── variables.tf
└── templates
    ├── gvars_all.tpl
    └── hosts.tpl
```

***
### Live directory:
```
├── live
│   ├── config.tf
│   ├── data.tf
│   ├── outputs.tf
│   ├── project-ec2.tf
│   ├── project-network.tf
│   ├── project-s_groups.tf
│   ├── project-ssh_keys.tf
│   ├── secret.auto.tfvars
│   ├── secret.auto.tfvars_reference
│   ├── terraform.tfstate
│   ├── terraform.tfstate.backup
│   └── variables.tf
```

- #### config.tf
	 Contain main configuration that invoke terraform code
	```hcl
	 terraform {
		required_providers {
		    aws = {
		      source  = "hashicorp/aws"
		      version = "~> 4.0"
		    }
		}
	 }
	
	 provider "aws" {
	   shared_config_files       = ["/home/user/.aws/config"]
	   shared_credentials_files  = ["/home/user/.aws/credentials"]
	  
	   region = "eu-central-1"
	 }
	```

- #### data.tf
	 There is data source that contain cloud-init script and set variables used it it
		Variables of init-script are terraform templates. 
	
	 ```hcl
	# set data source that contains cloud-init script for ec2 user_data
	data "cloudinit_config" "ansible_master" {
	    part {
	        content_type = "text/cloud-config"
	        content = templatefile("../scripts/ansible_master.yml", {
			# set variables that will be used ini cloud-init script
			       # set templatefile and variables to it
			hosts: templatefile("../templates/hosts.tpl", {
				dns_ips       = module.dns.private_ips
				webserver_ips = module.webserver.private_ips
				user_ips      = module.user.private_ips  
			})
			gvars_all: templatefile("../templates/gvars_all.tpl", {
				dns_ips        = module.dns.private_ips
				webserver_ips  = module.webserver.private_ips
				cidr_block    = module.dns_net.subnet_cidr
			})
			playbook: var.dns_stack_playbook
	```


- #### outputs.tf 
	 Contain values that will be show after terraform apply. (ec2 names and addresses)

	 ```hcl
	# outputs that will be shown in cli after terraform apply

	output "dns_vpc_id" {
	    value = module.dns_net.vpc_id
	}
	
	output "dns_subnet_id" {
	    value = module.dns_net.subnet_id
	}

	# outputs below generated from ../modules/ec2/ouptuts|ec2_ip 
	output "ec2_ansible-master_ip" {
	    value = module.ansible-master.ec2_ip
	}
	
	output "ec2_dns_ip" {
	    value = module.dns.ec2_ip
	}

	output "ec2_user_ip" {
	    value = module.user.ec2_ip
	}
	```

- #### secret.auto.tfvars
	 File contain sensitive variables, auto.tfvars extension mean that terraform will load it.
	 But you need to set them in variables.tf

	 ```hcl
	# secret.auto.tfvars
	cidr_1            = "1.1.1.0/24"
	public_ssh_test   = "ssh-rsa AAAAB3NzaC1yc2EA ...."
	```

	 ```hcl
	# variables.tf
	
	variable "cidr_1" {
	    type = string
	    sensitive = true
	} 
	
	variable "public_ssh_test" {
	    type = string
	    sensitive = true
	}
	```

- #### project-... .tf 
	 Files that contains modules
		 Each module contian values for variables from resource folder (source)

	 Example module for ec2 instance:
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

- #### terraform.tfstate / terraform.tfstate.backup
	 Files that contain current state of infrastructure
	 ! Automatically managed by terraform.

- #### variables.tf
	 Contain global variables that modules can use
	 ```hcl
	variable "availability_zone" {
	    type    = string
	    default = "eu-central-1b"
	}
	
	variable "public_ssh_test" {
	    type = string
	    sensitive = true
	}

	variable "cidr_1" {
	    type = string
	    sensitive = true
	}

	variable "dns_stack_playbook" {
	    type = string
	    default = "https://github.com/qqwerty222/dns-ngx-ansible.git"
	} 
	```

	 ```hcl
	# live/project-ec2.tf
	module "dns" {
		availability_zone = var.availability_zone
	}
	```

***
### Modules directory

```
├── modules
│   ├── aws_key_pair
│   │   ├── main.tf
│   │   ├── outputs.tf
│   │   └── variables.tf
│   ├── ec2
│   │   ├── main.tf
│   │   ├── outputs.tf
│   │   └── variables.tf
│   ├── network
│   │   ├── main.tf
│   │   ├── outputs.tf
│   │   └── variables.tf
│   └── security_groups
│       ├── main.tf
│       ├── outputs.tf
│       └── variables.tf
```
It contains resource dirs, with variables and outputs

- #### ec2
	 Contain aws_ec2_instance resource with variables and outputs.

- #### ec2/main.tf
	 There is ec2 resource file, each parameter you can find in terraform documentation.
	```hcl
	resource "aws_instance" "ec2" {
	# count invoke resources multiple times, depends from count of names
		count = length(var.list)
	
		ami           = var.ami
		instance_type = var.instance_type
		availability_zone = var.availability_zone

		subnet_id  = var.subnet_id
		# if multiple ec2 is running, you can ask them by index 
		private_ip = element(var.private_ip, count.index)
		associate_public_ip_address = var.associate_public_ip_address
	
		private_dns_name_options {
		enable_resource_name_dns_a_record = var.enable_dns_arec
		}

		key_name   = var.key_name  
		vpc_security_group_ids = var.sec_group_ids

		user_data = var.provision_conf
		
	# provisioner is module that ssh to instance while initialization
	# in this case it is sends ssh-key from local machine into remote 

	provisioner "file" {
		source = var.source_local
		destination = var.dest_remote

	# terraform will continue running, even if provisioner failed  
		on_failure = continue
	}
	connection {
		type = "ssh"
		host = self.public_ip
		user = "ubuntu"
		private_key = var.private_key_to_provision
	}
    
	# "Created" will be same for all instances
	# "Name" will be assigned depending on position in list of names

	tags = {
		Name    = element(var.list, count.index)
		Created = "Terraform"
	}  
	```
	 But make new resource for new ec2 every time is bad idea. That is why i use modules.

	 In module you can set parameters you need, and create multiple ec2 instances.
	 To make it possible i use variables.

	 That is how parameter ami get value  ( ec2.resource.ami < ec2.var.ami < module.ec2."value")
	 ```hcl
	# live/project-ec2.tf
	module "dns" {
		source("../modules/ec2")
		...
		ami = "ami-03e08697c325f02ab"
		...
	}

	# modules/ec2/variables.tf
	variable "ami" {
	    type    = string
	    default = null
	}
	
	# modules/ec2/main.tf
	resource "aws_instance" "ec2" {
		 ...
		 ami = var.ami
		 ...
	}
	```

- #### ec2/variables.tf
	 Define variables that will be used in module.
	 ! In module you can set only vars defined there.
	 ! Make sure that var names in module are equal with nemes in variables.tf
	```hcl
	# variables below will be filled in modules ../live/project-ec2

	variable "availability_zone" {
		type    = string
		default = null
	}

	#-----List-------
	# list of names, to run multiple ec2 from one module
	variable "list" {
	    type = list
	    default = null
	}
	
	#-----Network-----
	variable "subnet_id" {
	    type    = string
	    default = null
	}
	...
	```

- #### ec2/outputs.tf
	 There you can define values that you can get from another modules
	 like this: " module.ec2.ec2_ip "
	 ```hcl
	# return list (Name: name, Pub_ip: 1.1.1, Pr_ip: 1.1.1) for each instance
	output "ec2_ip" {
		value = [
			for x in aws_instance.ec2:
			"Name: ${x.tags.Name} Public_ip: ${x.public_ip} ... "
	    ]
	}
	
	# return list with private_ips
	output "private_ips" {
	    value = var.private_ip
	}
	```

### Another dirs in module/ have same structure and purpose

***
### Cloud-init script

Is yaml script that running while ec2 initialization.
Automatically install git and ansible, clone github repo with ansible playbook 
And create hosts and group_vars/all files in playbook directory. 

! write_files module runs before any other. 
! that is why it creates hosts and group_vars/all files in temporary folder and after move into dir      ! with cloned github repo

! hosts and group_vars/all are generated from templates
```cloud-config
#cloud-config
packages:
  - git
  - ansible

write_files:
  - path: /run/tmp/hosts
    owner: root:root
    permissions: "0644"
    content: "${hosts}"
  
  - path: /run/tmp/group_vars/all
    owner: root:root
    permissions: "0644"
    content: "${gvars_all}"

runcmd:
  - git clone "https://github.com/qqwerty222/dns-ngx-ansible.git" /run/playbook
  - mv /run/tmp/hosts /run/playbook/
  - mv /run/tmp/group_vars/all /run/playbook/group_vars/
```

To input this config into user_data parameter in ec2 instance you should use data module
```
# set data source that contains cloud-init script for ec2 user_data
data "cloudinit_config" "ansible_master" {
	part {
		content_type = "text/cloud-config"
		content = templatefile("../cloud-init_scripts/ansible_master.yml", {
	...
```

***
### Templates dir

Contain templates for hosts and group_variables files that will be used in ansible playbook.

- #### templates/hosts.tpl
	 This template filled hosts file, with list of ip addresses.
	 Using for loop make possible to input multiple ip addresses under 1 group.
	 "\\n" means new line 
	```
	[dns_servers]  
	%{ for ip in dns_ips ~}
	\n${ip}
	%{endfor ~}
	
	[webservers]
	%{ for ip in webserver_ips ~}
	\n${ip}
	%{endfor ~}
	
	[users]
	%{ for ip in user_ips ~}
	\n${ip}
	%{endfor ~}
	```

- #### templates/gvars_all
	 Using for loop there is needed just because variables with ip are lists. 
	```
	dns_ips: %{ for ip in dns_ips ~} ${ip} %{endfor ~}       
	\nwebserver_ips: %{ for ip in dns_ips ~} ${ip} %{endfor ~} 
	\ncidr_block: ${cidr_block}
	```

- #### values for hosts.tpl and gvars_all.tpl are defined in live/data.tf
	 ```
	        ...
				hosts: templatefile("../templates/hosts.tpl", {
					dns_ips       = module.dns.private_ips
					webserver_ips = module.webserver.private_ips
					user_ips      = module.user.private_ips  
				})
				
				gvars_all: templatefile("../templates/gvars_all.tpl", {
					dns_ips        = module.dns.private_ips
					webserver_ips  = module.webserver.private_ips
					cidr_block    = module.dns_net.subnet_cidr
				})
			...
	```

