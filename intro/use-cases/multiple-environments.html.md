---
title: "Managing Multiple Environments (Stage, QA, Prod)"
---

# Managing Multiple Environments

Terraform in Atlas makes it easy to reuse configurations and manage multiple environments.
Common configurations should be written as [modules](http://terraform.io/docs/modules/index.html),
and then referenced in the main Terraform file for each environment. For example, the usual tree
for managing multiple environments is:

	- prod
		- main.tf
		- .terraform
			- terraform.tfstate
		- prod.tfvars
	- qa
		- main.tf
		- .terraform
			- terraform.tfstate
		- qa.tfvars
	- stage
		- main.tf
		- .terraform
			- terraform.tfstate
		- stage.tfvars
	- module-vpc
	- module-web
	- module-db

Then, in the `main.tf` in each of the environments, you can easily reference
the modules and their output:

	module "vpc" {
	  source = "../module-vpc"
	}

	resource "aws_security_group" "allow" {
	  name = "allow"
	  vpc_id = "${module.vpc.vpc_id}"

	  // allow traffic for TCP 22
	  ingress {
	      from_port = 22
	      to_port = 22
	      protocol = "tcp"
	      cidr_blocks = ["0.0.0.0/0"]
	  }
	}

Each environment should setup [remote state storage in Atlas](http://terraform.io/docs/state/remote.html) separately:

	$ terraform remote config -backend-config="name=hashicorp/prod"
	$ terraform remote config -backend-config="name=hashicorp/qa"
	$ terraform remote config -backend-config="name=hashicorp/stage"

And then [push the configurations to Atlas](http://terraform.io/docs/commands/push.html) so Terraform can be run remotely:

	$ terraform push -name="hashicorp/prod"
	$ terraform push -name="hashicorp/qa"
	$ terraform push -name="hashicorp/stage"

With this setup, any time you make a change to a shared module, the update
propogates to all the environments to ensure parity.
