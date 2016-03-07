---
title: "Consul Key/Value in Atlas"
---

# Consul Key/Value in Atlas

Consul [supports storing key/value
data](https://www.consul.io/intro/getting-started/kv.html) for configuration.
You can manage K/V data for SCADA-connected Consul clusters from within Atlas.

The rest of this document assumes that you have already [connected your Consul
cluster to SCADA](https://www.consul.io/docs/guides/atlas.html).

## Editing

In the **Status** view for your environment, select **K/V** (next to
**Services** and **Nodes**) for a datacenter.

Existing key paths will be listed. Click any path to descend into that path and
see child keys and prefixes. Your current path is shown at the top of the
page.

To add a key, click **Add Key**. A key and value are required. Flags are
optional metadata, and are not used by Consul or Atlas.

To edit or delete a key, click on **Edit Key**. You can then modify the key's
value and flags, or delete the key.

## ACLs

Atlas supports access-control lists for restricting K/V access for different
Atlas teams. You can access the Consul K/V ACLs UI by selecting "ACLs" for a
SCADA-connected environment.

To get started, add an ACL path that you want to grant access for. An empty
path is equivalent to the Consul K/V root.

For each ACL path, teams can be added with a given permission level. The
available permission levels are `read`, `write`, and `admin`:

* `read` - Team members can read the data for any key with this prefix.
* `write` - Members can create keys in this prefix, and edit the values for
existing keys.
* `admin` - Allows team members to delete keys in this key prefix.

Access is determined by most-permissive. For instance, if a team has write
access to `foo` and read access to `foo/bar`, then that team effectively has
write access to `foo/bar` as well.

Teams shown in the Consul K/V ACLs UI can be managed by visiting [the Settings
page](/settings) for your organization.

> The Consul K/V ACLs in Atlas are not related to the [Consul ACL
system](https://www.consul.io/docs/internals/acl.html).
