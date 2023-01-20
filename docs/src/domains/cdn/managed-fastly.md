---
title: "Managed Fastly CDN"
sidebarTitle: "Managed Fastly CDN"
weight: 2
description: Bring your content closer to users with a Fastly CDN fully managed by Platform.sh.
tier:
  - Elite
  - Enterprise
---

Instead of starting your own Fastly subscription and [managing your CDN yourself](./fastly.md),
you can take advantage of a Fastly CDN provided by Platform.sh.
For example, Dedicated projects include a managed Fastly CDN by default.
These CDNs are exclusively set up and managed by Platform.sh.

To modify any settings for a managed Fastly CDN,
[open a support ticket](https://console.platform.sh/-/users/~/tickets/open).
To add a managed Fastly CDN to your project,
[contact sales](https://platform.sh/contact/).

### Domain control validation
 
When you request for a new domain to be added to your Fastly service,
Platform.sh support provides you with a [`CNAME` record](../../domains/steps/dns.md) for domain control validation.
To add this `CNAME` record to your domain settings,
see how to [configure your DNS provider](../steps/_index.md#3-configure-your-dns-provider).

### Transport Layer Security (TLS) certificates
 
By default, Enterprise and Elite plans include two [TLS certificates](../../other/glossary.md#transport-layer-security-tls),
an apex and a wildcard one.
This allows for encryption of all traffic between your users and your app.
 
If you use a Fastly CDN provided by Platform.sh, 
you can provide your own third-party TLS certificates for an additional fee.
If you don't have one, set up a [mount](../../create-apps/app-reference.md#mounts)
that isn't accessible to the web.
Use an environment with access limited to Platform.sh support and trusted users.
[Transfer](../../development/file-transfer.md) each certificate, its unencrypted private key, 
and the intermediate certificate to the mount.
 
Note that when you add your own third-party TLS certificates,
you are responsible for renewing them in due time.
Failure to do so may result in outages and compromised security for your site.
 
If you need an Extended Validation TLS certificate, 
you can get it from any TLS provider.
To add it to your CDN configuration, [create a support ticket](../../overview/get-support.md#create-a-support-ticket).