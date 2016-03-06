---
title: "Starting Packer Builds in Atlas"
---

# Starting Packer Builds in Atlas

Packer builds can be started in Atlas in two ways: `packer push`
to upload the template and directory or via a GitHub connection that retrieves
the contents of a repository after changes to the default branch (usually
master).

### Packer Push

Packer `push` is a [Packer command](https://packer.io/docs/command-line/push.html)
that packages and uploads a Packer template and directory to Atlas. This then creates a build
in Atlas, which performs `packer build` against the uploaded template
and packaged directory.

The directory is included in order to run any associated provisioners,
builds or post-processors that all might use local files. For example,
a shell script or set of Puppet modules used in a Packer build needs
to be part of the upload for Packer to be run remotely.

By default, everything in your directory is uploaded as part of the push.

However, it's not always the case that the entire directory should be uploaded. Often,
temporary or cache directories and files like `.git`, `.tmp` will be included by default. This
can cause Atlas to fail at certain sizes and should be avoided. You can
specify [exclusions](https://packer.io/docs/templates/push.html#exclude) to avoid this situation.

Packer also allows for a [VCS option](https://packer.io/docs/templates/push.html#vcs)
that will detect your VCS (if there is one) and only upload the files that are tracked by the VCS.
This is useful for automatically excluding ignored files. In a VCS
like git, this basically does a `git ls-files`.


### GitHub Import

Optionally, GitHub can be used to import Packer templates and configurations,
automatically building when changes are merged into the default branch.

When used within an organization, this can be extremely valuable for keeping
differences in environments and last mile changes from occurring before an
upload to Atlas.

This works by first connecting your [Build Configuration](/help/glossary#build-configuration) to the target
GitHub repository and then pushing changes to the default branch.
