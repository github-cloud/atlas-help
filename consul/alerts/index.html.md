---
title: "About Consul Alerts"
---

# About Consul Alerts

[Consul](https://consul.io) can be connected to Atlas to automatically provide
alerts to several different [notification
methods](/help/consul/alerts/notification-methods), including Slack, email,
HipChat and Pagerduty.

The rest of this document assumes that you have already [connected your Consul
cluster to SCADA](https://www.consul.io/docs/guides/atlas.html), and have
defined one or more [Consul Health
Checks](https://www.consul.io/docs/agent/checks.html).

## Team Notification Methods

Alerts are sent to notification methods associated with teams, such as
"Engineering Slack" or "Ops Pagerduty". Teams and their notification methods
can be managed by visiting [the Settings page](/settings) for your
organization.

You must have at least one team with configured notification
methods to receive alerts.

## Alert Preferences

Alert preferences are configured by visiting the **Integrations** page on any
Consul environment.

There are 3 types of alert preferences:

* **Logical services** are triggered when a percentage of a service is unhealthy threshold across your Consul cluster.
* **Services** are triggered when a service is unhealthy on any node
* **Nodes** are triggered when a node is unhealthy.

In addition, you can receive **SCADA Connection** alerts when Atlas can no
longer communicate with your Consul cluster.

Each alert preference has fields for matching nodes/service IDs/check IDS, a
status threshold, team destinations, and the ability to be temporarily muted.
Logical services also have a configurable unhealthy threshold.

### Alert Matching with Regular Expressions

The **Node**, **Check ID**, and **Service ID** fields support [regular
expressions](https://en.wikipedia.org/wiki/Regular_expression) for matching
alerts, and default to `.*` (match anything, including empty). For instance, if
you wanted to create an alert preference which matches both `database-primary`
and `database-secondary`, you could use the regular expression `database-.+`.

There are a few rules which Atlas uses while matching regular expressions to
keep in mind:

* **Case insensitive** - `database` will match `DATABASE`
* **Implicit being/end** - `database` is equivalent to `^database$`, and will not match `database-primary`

#### Examples

| Regular Expression | Will Match | Will Not Match |
|:-:|:-:|:-:|
| `.*` | Anything, including empty strings | |
| `.+` | `database`, <br> `web` | Empty strings |
| `database` | `database` | `database-primary` |
| `database-.+` | `database-primary`, <br> `database-secondary` | `database` |
| `i-\w{8}` | `i-abcd1234` | `i-0123456789abcdef0` |
| `i-\w{8,17}` | `i-abcd1234`, <br> `i-0123456789abcdef0` | *n/a* |
| `node-10(-\d{1,3}){3}` | `node-10-12-34-56` | `node-192-12-34-56` |

### Status Thresholds

Alerts can be triggered with a status of **`passing`**, **`warning`**, or
**`critical`**.

**Node** and **Service** alerts can be filtered based on their status:

* **any** will match any alerts.
* **warning, critical** will only match slerts with a status of **`warning`**
  or **`critical`**.
* **critical** will only match **`critical`** alerts.

**Logical Service** alert preferences only support **`any`** or **`critical
only`**.

**SCADA Connection** alerts are always sent.

### Team Destinations

In order to receive alerts, the alert preference must have at least one team
destination configured, such as "Engineering Slack (#engineering)" or "Ops
Pagerduty".  Team destinations can be configured by visiting [the Settings
page](/settings) for your organization.

If multiple alert preferences match the same alert, each team destination will
only notified once.

### Muting

Any alert preference can be muted for preset durations. While an alert
preference is muted, the alert preference will not match any alerts, and not
generate any notifications for configured team destinations.

Note that if multiple alert preferences match an alert and not all of them are
muted, the non-muted preferences will still trigger a notification for the
respective team destinations.
