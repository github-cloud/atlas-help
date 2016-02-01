---
title: "Application Compilation in Atlas"
---

# Application Compilation in Atlas

Atlas can compile an uploaded version of an application for deployment
by injecting the compiled artifact into the environment of a Packer
build, allowing provisioners to move the application artifact
onto the filesystem of the target machine image.

This is a separate step from the build step handled by Packer primarily
in order to avoid compilation and development dependencies being installed
on a production image, as well as placing the compilation responsibility
on the application developer and those responsible or the changes
to the application.

In order to compile an application, compilation must be enabled in the
applications settings and a compile template must be included alongside
the application source.

## Compile Templates

Compile templates are simply Packer templates with a specific set
of post-processors, with no restrictions to what builders
or provisioners are used. Currently, compile templates must contain
a single builder.

Because compilations are performed by Packer templates, the environment
and tools used to perform the compilation is extremely flexible, allowing
any platforms, OSes, dependencies that [Packer and Atlas support](/help/packer/builds/build-environment#supported-builders)
to be used.

### Choosing a Build Environment

Depending on what type of application is being compiled, you can select
the Packer build to use by specifying it in the `builders` stanza. The
example below uses Docker with an Ubuntu image, allowing us to compile
in the Ubuntu operating system without waiting for the slow startup
of a virtual machine.

### Variables

There a several variables available in the compile environment
that can be used to reference artifacts, configure compilation or
name the resulting archives created. This can help ease sharing
of compile templates and remove special casing from each template.

- `ATLAS_COMPILE_ID` - This is a unique identifier for this compile (e.g. `"3"`)
- `ATLAS_APPLICATION_NAME` - This is the name of the application connected to
  the Packer build (e.g. `"myapp"`).
- `ATLAS_APPLICATION_SLUG` - This is the full name of the application connected
  to the Packer build (e.g. `"company/myapp"`).
- `ATLAS_APPLICATION_USERNAME` - This is the username associated with the
  application connected to the Packer build (e.g. `"sammy"`)
- `ATLAS_APPLICATION_VERSION` - This is the version of the application connected
  to the Packer build (e.g. `"2"`).
- `ATLAS_APPLICATION_GITHUB_BRANCH` - This is the name of the branch that the
  associated application version was ingressed from (e.g. `master`).
- `ATLAS_APPLICATION_GITHUB_COMMIT_SHA` - This is the full commit hash
  of the commit that the associated application version was ingressed from
  (e.g. `"abcd1234..."`).
- `ATLAS_APPLICATION_GITHUB_TAG` - This is the name of the tag that the
  associated application version was ingressed from (e.g. `"v0.1.0"`).

For any of the `GITHUB_` attributes, the value of the environment variable will
be the empty string (`""`) if the resource is not connected to GitHub or if the
resource was created outside of GitHub (like using `vagrant push`).

### Post-Processors

There are two required post-processors for compile templates. The first,
the `artifice` post-processor, selects a file and passes it to the
`atlas` post-processor which uploads the compiled archive to Atlas,
allowing it to be downloaded later during a build.

This archive needs to be downloaded from the builder environment where
the compile occured to the local environment where Packer is running. This
is demonstrated below.

    {
      "type": "file",
      "source": "/tmp/compiled-app.tar.gz",
      "destination": "compiled-app.tar.gz",
      "direction": "download"
    }

The artifact can be stored as any type or under any artifact in Atlas,
as long as it is accessible later by the same user triggering builds
that use the artifact. It is worth noting, that one should use `.tar.gz` as their
artifact in order to properly to utilize the pipeline. When a Build Template is specified, Atlas
expects the Artifact of the compiled application to be a `.tar.gz` file. This archive is autoextracted
to a source directory `app/` on the Packer build specified.

### Complete Example

#### Application Compilation Template
This template is a working example for a project that installs and
compiles the application using a `Makefile` and outputs
a `.tar.gz` archive of the compiled application. It then uploads
the archive to the artifact in Atlas matching the name of the application,
as it uses the `ATLAS_APPLICATION_SLUG` environment variable, i.e `acmeinc/webapp`.

    {
      "variables": {
        "app_slug": "{{ env `ATLAS_APPLICATION_SLUG` }}"
      },
      "builders": [
        {
          "type": "docker",
          "image": "ubuntu:14.04",
          "commit": true
        }
      ],
      "provisioners": [
        {
          "type": "shell",
          "inline": [
            "apt-get -y update"
          ]
        },
        {
          "type": "file",
          "source": ".",
          "destination": "/tmp/app"
        },
        {
          "type": "shell",
          "inline": [
            "cd /tmp/app",
            "make"
          ]
        },
        {
          "type": "file",
          "source": "/tmp/compiled-app.tar.gz",
          "destination": "compiled-app.tar.gz",
          "direction": "download"
        }
      ],
      "post-processors": [
        [
          {
            "type": "artifice",
            "files": ["compiled-app.tar.gz"]
          },
          {
            "type": "atlas",
            "artifact": "{{user `app_slug` }}",
            "artifact_type": "archive"
          }
        ]
      ]
    }

#### Associated Packer Build Template
This template is a working example of how to use the resulting Artifact from the Application Compilation in an associated Packer Build Template. When Compile Application is enabled in Atlas, the compiled artifact will be injected into the Build Template specified. The artifact is accessible to the builder using the `app/` source directory. This directory will contain the extracted archive from the Compilation step:

    {
      "variables": {
        "aws_access_key": "{{ env `aws_access_key` }}",
        "aws_secret_key": "{{ env `aws_access_key` }}",
        "atlas_username": "{{ env `atlas_username` }}"
      },
      "builders": [
        {
          "type": "amazon-ebs",
          "access_key": "{{user `aws_access_key`}}",
          "secret_key": "{{user `aws_secret_key`}}",
          "region": "us-east-1",
          "source_ami": "ami-9a562df2",
          "instance_type": "t2.micro",
          "ssh_username": "ubuntu",
          "ami_name": "make-build {{timestamp}}"
        }
      ],
      "provisioners": [
        {
          "type": "file",
          "source": "app/",
          "destination": "/path/to/dest"
        }
      ],
      "post-processors": [
        {
          "type": "atlas",
          "artifact": "{{user `atlas_username`}}/make-build",
          "artifact_type": "amazon.image",
          "metadata": {
            "created_at": "{{timestamp}}"
          }
        }
      ],
      "push": {
        "name": "{{user `atlas_username`}}/make-build",
        "vcs": false
      }
    }
