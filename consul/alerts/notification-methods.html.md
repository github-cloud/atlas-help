---
title: "Alert Notification Methods"
---

# Alert Notification Methods

Atlas currently supports many different types of notification methods, outlined
below.

Notification methods can be managed by visiting the **Configuration** page for
your user or organization. Notification methods can also be overridden for a
specific build configuration, environment, or infrastructure.

## Email

An email can be set and receive alerts trigged by Atlas. Email
alerts include check status, output, as well as the
affected service or node.

To configure Email notifications:

1. Enter the email into the "Integrations" tab of the environment in Atlas
1. Optionally, test the alert

## HipChat

Notifications can be sent to a [HipChat](https://hipchat.com) room. There are
two ways to configure HipChat notifications:

### User Account

This API token can post to any room that the user account has access to, but
will be displayed as that user's name. You may want to create an "Atlas" user.

1. In your **Account Settings**, click [API
   Access](https://hipchat.com/account/api).
1. Enter a label for the new token, such as "Atlas".
1. Ensure the **Send Notification** scope is selected.
1. Click **Create**, and then copy the token from the confirmation page into
   your Atlas notification methods.

### Group Integration

The API token generated here will only have access to the room you chose during
creation, but you can customize the display name. You may want to create a
Group Integration for each room you intend to post to.

1. Create a [new integration](https://hipchat.com/admin/byo) for your HipChat
   group.
1. Select the room that this integration should post messages to.
1. Choose a name for the integration, such as "Atlas". This will be the
   username that messages are posted as.
1. Click **Create**, and then copy the token from the examples on the next page
   into your Atlas notification methods.

## Slack

Alert messages can be sent to a [Slack](https://slack.com) channel. Alert
types are color coded and show a brief snippet of relevant information.

To configure Slack:

1. Log into your Slack account and [create an incoming webhook](https://my.slack.com/services/new/incoming-webhook/)
1. Enter the webhook URL into the field provided in the "Integrations" tab of the environment
1. Optionally, test the alert

## PagerDuty

[PagerDuty](https://www.pagerduty.com) incidents can be automatically created
based on Consul Alerts triggered by Atlas. When the alert recovers, the
PagerDuty incident is resolved by Atlas.

> Only Consul Alerts are sent to PagerDuty

To configure PagerDuty:

1. Log in to your PagerDuty account
1. Visit **Configuration > Services** to choose an existing service API key, or
   to create a new one
1. Enter the service API key into the "Integrations" tab of the environment tab
   of the environment in Atlas
1. Optionally, test the alert.

## Webhook

Notifications can be sent to custom Webhook URLs to allow for integration
into third-party systems. An HTTP _Post_ will be sent with the Post _Body_
containing the JSON payload with the notification details. Example JSON
payloads are shown below.

> Note: The Webhook URL must be accessible from Atlas.

### Example Notification Payloads

### Applications

Example JSON payload for Application Notifications:

```
{
  "application_alert": {
    "description": "Compile started",
    "slug": "example-org/example-app",
    "version": 19,
    "message": "Update index.html",
    "status": "compile_started",
    "url": "https://atlas.hashicorp.com/example-org/applications/example-app/versions/19"
  }
}
```

Valid values for `status` are:

```
compile_started
compile_finished
compile_errored
job_started
job_finished
job_canceled
job_errored
```

### Consul Alerts

Example JSON payload for Alert Notifications:

```
{
  "consul_alert": {
    "environment": "example-org/example-environment",
    "datacenter": "example-environment",
    "type": "service",
    "key": "23a807b41eabd617a45beee28c0001a8",
    "number": 5,
    "status": "warning",
    "health_check": {
      "node": "example-node",
      "notes": "",
      "output": "",
      "status": "warning",
      "check_id": "service:example-service",
      "check_name": "Service 'example-service' check",
      "service_id": "example-service"
    },
    "logical_service": null
  }
}
```

Valid values for `status` are:

```
passing
warning
critical
```

### Packer Builds

Example JSON payload for Build Notifications:

```
{
  "packer_alert": {
    "build_configuration": "example-org/example-build",
    "number": 6,
    "status": "started",
    "url": "https://atlas.hashicorp.com/example-org/build-configurations/example-build/builds/6"
  }
}
```

Valid values for `status` are:

```
started
finished
canceled
errored
```

#### Terraform Runs

Example JSON payload for Run Notifications:

```
{
  "terraform_alert": {
    "environment": "example-org/example-environment",
    "message": "Queued manually in Atlas",
    "number": 48,
    "status": "applying",
    "url": "https://atlas.hashicorp.com/example-org/environments/example-environment/changes/runs/48"
  }
}
```

Valid values for `status` are:

```
planned
confirmed
discarded
applied
applying
errored
```
