---
title: "Getting Started with Atlas"
---

# Getting Started with Atlas

Atlas has a wide range of features that cover many use cases. It's important
to understand how Atlas is positioned before diving in.

In short, Atlas enables the HashiCorp toolset (Vagrant, Packer, Consul
and Terraform) to do things they could not do in isolation.

Because the toolset spans the entire timeline from development
to production, Atlas does too. This can lead to confusion, as
it's often the case that the goals of development teams differ from that
of operator teams.

When using Atlas, it's important to remember it attempts to serve those
different groups and contexts within an organization. Vagrant features, for example,
have a different user experience than Consul monitoring features.

## Tutorials

The following tutorials are currently available.

- __[Create a Vagrant Box with Packer](/tutorial/packer-vagrant)__. This tutorial helps
you create your first Vagrant box with Packer and Atlas. A great starting point for automating
box builds.
- __[Launch AWS infrastructure from GitHub with Terraform](/tutorial/terraform-github)__.
Automatically fork a GitHub repository with Terraform configuration and spin up real
infrastructure, all without touching the command line.

- __[Using Terraform with Atlas](/tutorial/terraform)__. Integrate Terraform
remote state storage, Terraform push and
watch as Atlas builds infrastructure.

## Further Reading

We recommend reading the following sections to continue gaining an understanding
of what Atlas can provide:

- __[Features](/help/intro/features-list)__ – A complete list of everything Atlas does
- __[Use cases](/help/intro/use-cases)__ – Examples of several places to start using Atlas
- __[Glossary](/help/glossary)__ – All of the terminology used by Atlas and HashiCorp products
