
***
### Get data from resource in same file
```hcl
*-----------------------------------------------------------------------------*
# every resource expose some attributes by default
# you can find this attributes in terraform docs ("Attributes Reference") block

##https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/vpc#attributes-reference

# one of the attributes that aws_vpc reference is "id"
resource "aws_vpc" "vpc" {
	...
    }
}

# to get attribute from resource in same file use:
#(aws_resource).(resource_name).(attribute)

resource "aws_subnet" "subnet" {
    vpc_id = aws_vpc.vpc.id
    ...
}
```

***
### Get data from local variable
```hcl
*-----------------------------------------------------------------------------*
# if variable is defined in same file you can get value using:
# var.(var_name)

variable "availability_zone" {
    type    = string
    default = "eu-central-1b"
}

resource "aws_instance" "ec2" {
	...
    availability_zone = var.availability_zone
	...
}

*-----------------------------------------------------------------------------*
# also you can accesss variable from another file (!!!but in current folder!!!)

├── ec2
│   ├── main.tf
│   ├── outputs.tf
│   └── variables.tf

# variables.tf
variable "availability_zone" {
    type    = string
    default = "eu-central-1b"
}

# main.tf
resource "aws_instance" "ec2" {
	...
    availability_zone = var.availability_zone
	...
}
```

***
### Define modules 
```hcl
*-----------------------------------------------------------------------------*
# Modules gives you an opportunity to configure resources without changing it.
# So you don't need to create new resource for new instance.

# as best practice you should have structure like this:
├── live
│   ├── config.tf
│   ├── data.tf
│   ├── outputs.tf
│   ├── project-ec2.tf
│   └── variables.tf
├── modules
│   ├── ec2
│   │   ├── main.tf
│   │   ├── outputs.tf
│   │   └── variables.tf

# "live" directory store parameters of your current configuration
# "terraform apply" is executed from it

# "modules" is directory that contain resource dirs
# "ec2"     is resource dir that contain resource configuration

# this one of the modules in project-ec2.tf 
# every module should contain source attribute with path to resource dir

# live/project-ec2.tf
module "dns" {
    source = "../modules/ec2"
    
    list = ["dns"]
    instance_type     = "t2.micro"
    ami               = "ami-03e08697c325f02ab"
    ...
}
```

### Variable transfer between modules and resources
```hcl
*-----------------------------------------------------------------------------*
# variables that module set are defined in modules/(resource dir)/variables.tf
# !!! make sure that names of vars in module are equal to names in variables.tf

# modules/ec2/variables.tf
variable "list" {
    type = list
    default = null
}
variable "instance_type" {
    type    = string
    default = null
}
variable "ami" {
    type    = string
    default = null
}

# main.tf is in one dir with variables.tf so it can use vars from them 

# modules/ec2/main.tf
resource "aws_instance" "ec2" {
    count = length(var.list)
    ami           = var.ami
    instance_type = var.instance_type
	...
}

# as result we get structure like that: 
# module>--->variables.tf>---->main.tf 
```

### Local output
```hcl
*-----------------------------------------------------------------------------*
# outputs is some data you get from resource or variable
# outputs from dir where you run terraform apply will be shown in cli

├── ec2
│   ├── main.tf
│   ├── outputs.tf
│   └── variables.tf

# outputs.tf 
output "ec2_ip" {
    value = aws_instance.ec2.public_ip

# result in cli after "terraform apply"
Outputs:
ec2_ip = "13.123.432.123"
```

## Get output from another module
```hcl
*-----------------------------------------------------------------------------*
# If you use modules, as usual you need transfer some attributes between them
# Defining outputs is the best way to do this

├── live
│   ├── config.tf
│   ├── data.tf
│   ├── outputs.tf
│   ├── project-ec2.tf
│   ├── project-s_groups.tf
│   └── variables.tf
├── modules
│   ├── ec2
│   │   ├── main.tf
│   │   ├── outputs.tf
│   │   └── variables.tf
│   └── security_groups
│       ├── main.tf
│       ├── outputs.tf
│       └── variables.tf

# Imagine you have 2 resources "aws_instance" and "aws_security_groups"
# And you want to put icmp security group id into aws instance

# live/project-s_groups.tf
module "icmp" {
    source = "../modules/security_groups"
    name = "icmp"

    description = "allow icmp(ping) traffic"

    ingress_to_port   = -1
    ingress_from_port = -1
    ingress_protocol  = "icmp"
    ingress_cidr      = "0.0.0.0/0"

    egress_to_port    = -1
    egress_from_port  = -1
    egress_protocol   = "icmp"
    egress_cidr       = "0.0.0.0/0"
}

# Firstly define output 
# In terraform documentation you can see that "id" is on of the default attributes

#modules/security_groups/outputs.tf
output "sec_group_id" {
    value = aws_security_group.sec_group.id
}

# Next step is just assign variable of ec2 module with output of s_groups module
# You can use next syntax: module.(module_name).(output_name)

#live/project-ec2.tf
module "dns" {
    ...
    sec_group_ids = [module.icmp.sec_group_id]
	...
}
```