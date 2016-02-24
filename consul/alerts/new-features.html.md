---
title: "New Features in Consul Alerts"
---

# Team-based Alerting

Previously, notification methods were defined on an **Environment** or
inherited from the **Organization**.

In the near future, we will introduce more customizations to alerts
preferences, including the ability to send alerts to specific teams and
notification methods. For example:

> Send all service alerts for "redis" to the Ops team Slack channel

Teams may appear in your organization with the prefix "alerts-". These are
created automatically by HashiCorp to ensure a smooth transition and service
continuity when these new features are activated.
