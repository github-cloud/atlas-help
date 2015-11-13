---
title: "Using Vagrant Share with WordPress"
---

# Using Vagrant Share with WordPress

Vagrant Share works with WordPress, but only when relative URLs are used.

By default, WordPress uses absolute URLs. This means
that theme HTML may contain `http://127.0.0.1/asset.png` instead
of `/asset.png`.

Whoever is visiting your share via the public URL unfortunately can't
route to `127.0.0.1` as an address. However, if the HTML used
`/asset.png` as the asset URL, the browser would automatically assume it's on the same
host as the Vagrant Share URL, which it can access.

To enable relative URLs and make this work, you need to install the
[Relative URL plugin](https://wordpress.org/plugins/relative-url/).
The Relative URL plugin applies the `wp_make_link_relative` function to
links to convert them to relative URLs, allowing access over Vagrant
Share.

