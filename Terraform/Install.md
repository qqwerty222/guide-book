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

## After install connect with credentials from aws cli this way
```
provider "aws" {
  shared_config_files       = ["/home/user/.aws/config"]
  shared_credentials_files  = ["/home/user/.aws/credentials"]

  region = "eu-central-1"
}
```