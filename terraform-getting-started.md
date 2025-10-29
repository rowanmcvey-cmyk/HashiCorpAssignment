# Getting started with Terraform

Terraform is the most popular language for defining and provisioning infrastructure as code (IaC).

## Learning objectives

After you finish this lesson, you will be able to:
- Install Terraform on your local machine
- Create a basic Terraform configuration file
- Initialize Terraform and install a Terraform provider
- Provision and destroy resources using Terraform

To install Terraform, go to [Terraform.io](https://www.terraform.io/downloads.html) and find the appropriate installation package for your system. Download and install it.

## Create a Terraform configuation file

Create a new directory on your local machine to store your Terraform configuration files.

```shell
$ mkdir terraform-demo
$ cd terraform-demo
```

Next, create a file for your Terraform configuration code.

```shell
$ touch main.tf
```

Open the file with a text editor and paste the following lines into the file.

```hcl
terraform {
  required_providers {
    docker = {
      source = "kreuzwerker/docker"
    }
  }
}
provider "docker" {
    host = "unix:///var/run/docker.sock"
}
resource "docker_container" "nginx" {
  image = docker_image.nginx.image_id
  name  = "training"
  ports {
    internal = 80
    external = 8000
  }
}
resource "docker_image" "nginx" {
  name = "nginx:latest"
}
```
## Provision and destroy resources using Terraform

Initialize Terraform with the `init` command. Terraform will install the Docker provider, as specified in the configuration file you created in the previous step.

```shell
$ terraform init
```

If the `init` command runs successfully, should see this response:
```shell
Initializing the backend...
Initializing provider plugins...
- Finding latest version of kreuzwerker/docker...
- Installing kreuzwerker/docker v3.6.2...
- Installed kreuzwerker/docker v3.6.2 (self-signed, key ID BD080C4571C6104C)
Partner and community providers are signed by their developers.
If you'd like to know more about provider signing, you can read about it here:
https://developer.hashicorp.com/terraform/cli/plugins/signing
Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```
If the `init` command did not run successfully, correct any errors and run it again.

After you have successfully initialized your Terraform configuration, provision the resource with the `apply` command.

```shell
$ terraform apply
```

The command will take up to a few minutes to run. If it runs successfully, you should see this output:

```shell
Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # docker_container.nginx will be created
  + resource "docker_container" "nginx" {
      + attach                                      = false
      + bridge                                      = (known after apply)
      + command                                     = (known after apply)
      + container_logs                              = (known after apply)
      + container_read_refresh_timeout_milliseconds = 15000
      + entrypoint                                  = (known after apply)
      + env                                         = (known after apply)
      + exit_code                                   = (known after apply)
      + hostname                                    = (known after apply)
      + id                                          = (known after apply)
      + image                                       = (known after apply)
      + init                                        = (known after apply)
      + ipc_mode                                    = (known after apply)
      + log_driver                                  = (known after apply)
      + logs                                        = false
      + must_run                                    = true
      + name                                        = "training"
      + network_data                                = (known after apply)
      + network_mode                                = "bridge"
      + read_only                                   = false
      + remove_volumes                              = true
      + restart                                     = "no"
      + rm                                          = false
      + runtime                                     = (known after apply)
      + security_opts                               = (known after apply)
      + shm_size                                    = (known after apply)
      + start                                       = true
      + stdin_open                                  = false
      + stop_signal                                 = (known after apply)
      + stop_timeout                                = (known after apply)
      + tty                                         = false
      + wait                                        = false
      + wait_timeout                                = 60

      + healthcheck (known after apply)

      + labels (known after apply)

      + ports {
          + external = 8000
          + internal = 80
          + ip       = "0.0.0.0"
          + protocol = "tcp"
        }
    }

  # docker_image.nginx will be created
  + resource "docker_image" "nginx" {
      + id          = (known after apply)
      + image_id    = (known after apply)
      + name        = "nginx:latest"
      + repo_digest = (known after apply)
    }

Plan: 2 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: 
```
Type `yes` and hit ENTER.

After confirming, you should see output similar to this (the exact id values may vary):

```shell
docker_image.nginx: Creating...
docker_image.nginx: Creation complete after 6s [id=sha256:9d0e6f6199dcb6e045dad103064601d730fcfaf8d1bd357d969fb70bd5b90decnginx:latest]
docker_container.nginx: Creating...
docker_container.nginx: Creation complete after 1s [id=75a679d73f1a16faf64578e0592ebe202055a4d02110a981278d080c763daef3]

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
```

Finally, destroy the infrastructure.

```shell
$ terraform destroy
```
You should see this output:

```shell
docker_image.nginx: Refreshing state... [id=sha256:9d0e6f6199dcb6e045dad103064601d730fcfaf8d1bd357d969fb70bd5b90decnginx:latest]
docker_container.nginx: Refreshing state... [id=75a679d73f1a16faf64578e0592ebe202055a4d02110a981278d080c763daef3]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # docker_container.nginx will be destroyed
  - resource "docker_container" "nginx" {
      - attach                                      = false -> null
      - command                                     = [
          - "nginx",
          - "-g",
          - "daemon off;",
        ] -> null
      - container_read_refresh_timeout_milliseconds = 15000 -> null
      - cpu_shares                                  = 0 -> null
      - dns                                         = [] -> null
      - dns_opts                                    = [] -> null
      - dns_search                                  = [] -> null
      - entrypoint                                  = [
          - "/docker-entrypoint.sh",
        ] -> null
      - env                                         = [] -> null
      - group_add                                   = [] -> null
      - hostname                                    = "75a679d73f1a" -> null
      - id                                          = "75a679d73f1a16faf64578e0592ebe202055a4d02110a981278d080c763daef3" -> null
      - image                                       = "sha256:9d0e6f6199dcb6e045dad103064601d730fcfaf8d1bd357d969fb70bd5b90dec" -> null
      - init                                        = false -> null
      - ipc_mode                                    = "private" -> null
      - log_driver                                  = "json-file" -> null
      - log_opts                                    = {} -> null
      - logs                                        = false -> null
      - max_retry_count                             = 0 -> null
      - memory                                      = 0 -> null
      - memory_swap                                 = 0 -> null
      - must_run                                    = true -> null
      - name                                        = "training" -> null
      - network_data                                = [
          - {
              - gateway                   = "172.17.0.1"
              - global_ipv6_prefix_length = 0
              - ip_address                = "172.17.0.3"
              - ip_prefix_length          = 16
              - mac_address               = "22:8b:25:8a:d1:a3"
              - network_name              = "bridge"
                # (2 unchanged attributes hidden)
            },
        ] -> null
      - network_mode                                = "bridge" -> null
      - privileged                                  = false -> null
      - publish_all_ports                           = false -> null
      - read_only                                   = false -> null
      - remove_volumes                              = true -> null
      - restart                                     = "no" -> null
      - rm                                          = false -> null
      - runtime                                     = "runc" -> null
      - security_opts                               = [] -> null
      - shm_size                                    = 64 -> null
      - start                                       = true -> null
      - stdin_open                                  = false -> null
      - stop_signal                                 = "SIGQUIT" -> null
      - stop_timeout                                = 0 -> null
      - storage_opts                                = {} -> null
      - sysctls                                     = {} -> null
      - tmpfs                                       = {} -> null
      - tty                                         = false -> null
      - wait                                        = false -> null
      - wait_timeout                                = 60 -> null
        # (7 unchanged attributes hidden)

      - ports {
          - external = 8000 -> null
          - internal = 80 -> null
          - ip       = "0.0.0.0" -> null
          - protocol = "tcp" -> null
        }
    }

  # docker_image.nginx will be destroyed
  - resource "docker_image" "nginx" {
      - id          = "sha256:9d0e6f6199dcb6e045dad103064601d730fcfaf8d1bd357d969fb70bd5b90decnginx:latest" -> null
      - image_id    = "sha256:9d0e6f6199dcb6e045dad103064601d730fcfaf8d1bd357d969fb70bd5b90dec" -> null
      - name        = "nginx:latest" -> null
      - repo_digest = "nginx@sha256:b619c34a163ac12f68c1982568a122c4953dbf3126b8dbf0cc2f6fdbfd85de27" -> null
    }

Plan: 0 to add, 0 to change, 2 to destroy.

Do you really want to destroy all resources?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: 
```

Type `yes` and hit ENTER to confirm. Terraform will destroy the resources it had created earlier.

You should see this output:

```shell
docker_container.nginx: Destroying... [id=75a679d73f1a16faf64578e0592ebe202055a4d02110a981278d080c763daef3]
docker_container.nginx: Destruction complete after 0s
docker_image.nginx: Destroying... [id=sha256:9d0e6f6199dcb6e045dad103064601d730fcfaf8d1bd357d969fb70bd5b90decnginx:latest]
docker_image.nginx: Destruction complete after 1s

Destroy complete! Resources: 2 destroyed.
```
