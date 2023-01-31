Terraform state means current configuration. It can be stored in current directory, or in some remote directory.  

### Init terraform configuration
```
╭─bohdan@PF2FXPPG ~/docker/terraform-ansible
╰─$ terraform init                                        1 ↵

Initializing the backend...

Initializing provider plugins...
- Reusing previous version of kreuzwerker/docker from the dependency lock file
- Using previously-installed kreuzwerker/docker v2.13.0

Terraform has been successfully initialized!
...
```

***
### Check terraform configuration
```
╭─bohdan@PF2FXPPG ~/docker/terraform-ansible
╰─$ terraform validate                                                             
Success! The configuration is valid.
```

### Show terraform state
- There you can see  configuration about deployed resources
```
╭─bohdan@PF2FXPPG ~/docker/terraform-ansible
╰─$ terraform show
# docker_container.ubuntu:
resource "docker_container" "ubuntu" {
    attach            = false
    command           = [
        "/usr/sbin/sshd",
        "-D",
    ]
    cpu_shares        = 0
    entrypoint        = []
    env               = []
    ...

# docker_network.private_network:
resource "docker_network" "private_network" {
    attachable  = false
    driver      = "bridge"
    ...
```

***
### Show only resources that are managed
```
╭─bohdan@PF2FXPPG ~/docker/terraform-ansible
╰─$ terraform state list
docker_container.ubuntu
docker_network.private_network
```

***
### Check difference between deployed and current state

```
╭─bohdan@PF2FXPPG ~/docker/terraform-ansible
╰─$ terraform plan
...

Terraform will perform the following actions:

  # docker_container.ubuntu must be replaced
-/+ resource "docker_container" "ubuntu" {
      + bridge            = (known after apply)
      ~ command           = [
          - "/usr/sbin/sshd",
          - "-D",
        ] -> (known after apply)
      + container_logs    = (known after apply)
      - cpu_shares        = 0 -> null
      - dns               = [] -> null
```

***
### Deploy changes

```
╭─bohdan@PF2FXPPG ~/docker/terraform-ansible
╰─$ terraform apply
...
Plan: 1 to add, 0 to change, 1 to destroy.

docker_container.ubuntu: Destroying... [id=30ac769e0b2da2b4ef66453508d39c3bda396e94e3f69448dcbb005bbab23473]
docker_container.ubuntu: Destruction complete after 0s
docker_container.ubuntu: Creating...
docker_container.ubuntu: Creation complete after 0s [id=82dde4cbe259ce879b4ba22ad7a517622258c19011bbace57ba2a6b2355e21c4]

Apply complete! Resources: 1 added, 0 changed, 1 destroyed.

```

***
### Destroy configuration
```
╭─bohdan@PF2FXPPG ~/docker/terraform-ansible
╰─$ terraform destroy
...
Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # docker_container.ubuntu will be destroyed
  ...
```