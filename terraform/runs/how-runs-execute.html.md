---
title: "How Terraform Runs Execute in Atlas"
---

# How Terraform Runs Execute in Atlas

This briefly covers the internal process of running Terraform plan and
applies in Atlas. It is not necessary to know this information, but may be
valuable to help understand implications of running in Atlas or debug failing
runs.

## Steps of Execution

1. A set of Terraform configuration and directory of files is uploaded via Terraform Push or GitHub
1. Atlas creates a version of the Terraform configuration and waits for the upload
to complete. At this point, the version will be visible in the UI even if the upload has
not completed
1. Once the upload finishes, Atlas creates a run and queues a `terraform plan`
1. In the run environment, the package including the files and Terraform
configuration are downloaded
1. `terraform plan` is run against the configuration in the run environment
1. Logs are streamed into the UI and stored
1. The `.tfplan` file created in the plan is uploaded and stored
1. Once the plan completes, the environment is torn down and status is
updated in the UI
1. The plan then requires confirmation by an operator. It can optionally
be discarded and ignored at this stage
1. Once confirmed, the run then executes a `terraform apply` in a new
environment against the saved `.tfplan` file
1. The logs are streamed into the UI and stored
1. Once the apply completes, the environment is torn down, status is
updated in the UI and changed state is saved back to Atlas

Note: In the case of a failed apply, it's safe to re-run. This is possible
because Terraform saves partial state and can "pick up where it left off".
