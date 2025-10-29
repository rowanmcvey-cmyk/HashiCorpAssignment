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
  image = docker_image.nginx.latest
  name  = "training"
  ports {
    internal = 80
    external = 80
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

You should check for any errors. If the `init` command ran successfully, provision the resource with the `apply` command.

```shell
$ terraform apply
```

The command will take up to a few minutes to run and will display a message indicating that the resource was created.

Finally, destroy the infrastructure.

```shell
$ terraform destroy
```

Look for a message are the bottom of the output asking for confirmation. Type `yes` and hit ENTER. Terraform will destroy the resources it had created earlier.
