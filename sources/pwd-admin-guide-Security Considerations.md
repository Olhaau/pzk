---
title: Security Considerations – Administration Guide
keywords: source
---

# Security Considerations – Administration Guide

url:: https://docs.posit.co/ide/server-pro/access_and_security/security_considerations.html
up: [[sources]]

### IP access rules

Posit Workbench can be configured to deny access to specific IP addresses or ranges of addresses. Access rules are defined in the configuration file `/etc/rstudio/ip-rules`

Access rules are established using the `allow` and `deny` directives and are processed in order, with the first matching rule governing whether a given address is allowed or denied. For example, to allow only clients within the `192.168.1.0/24` subnet but also deny access to `192.168.1.10` you would use these rules:  

    #cb1-1deny    192.168.1.10
    #cb1-2allow   192.168.1.0/24
    #cb1-3deny    all
All clients outside of the specified subset are denied access because of the `deny all` rule at the end of the configuration.

Note that changes to the configuration will not take effect until the server is restarted.

### Frame origin

For security reasons, Workbench will not load inside a browser frame (such as a frameset or iframe) by default. You can modify this behavior by using the `www-frame-origin` option. For example, if you would like to host Workbench inside a browser frame at `example.com`, you can tell Workbench to allow this as follows:  

```
/etc/rstudio/rserver.conf
```

    #cb2-1www-frame-origin=example.com

There are several special values available for the `www-frame-origin` option:

|-----------------|--------------------------------------------------------------------------------------------|
| `none`          | The default; do not allow Workbench to load in any frame.                                  |
| `same`          | Allow Workbench to load in a frame if it has the same origin (host and port) as Workbench. |
| `any`           | Allow Workbench to load in a frame from any origin (not recommended)                       |
| *my-domain.com* | Allow Workbench to load in a frame at *my-domain.com*                                      |

To interact programmatically with Workbench in an iframe, see the [Tutorial API](https://docs.posit.co/ide/server-pro/access_and_security/../tutorial_api/tutorial_api.html).

### CSRF attack mitigation

To help mitigate against CSRF attacks, Workbench can automatically reject any request originating from an `Origin` or `Referer` that does not match the `Host`, `X-Forwarded-Host`, `Forwarded: host` or `X-RStudio-Request` headers. To enable this check, add the following configuration:  

```
/etc/rstudio/rserver.conf
```

    #cb3-1www-enable-origin-check=1

In some cases, such as if running behind a proxy that you cannot modify, this check may be too strict, and can prevent access to Workbench, causing requests to return a 400 status. In such cases, it is recommended that you disable the check. Origin checking is currently disabled by default, but will likely be enabled by default in a future version of Workbench.

You may wish to consider some origins to be safe in all cases, causing Workbench to consider such `Origin` or `Referer` values to be allowed instead of being rejected, even if they do not match a Host header. To specify such origins, add each of them as a `www-allow-origin` setting in `rserver.conf`. For example:  

```
/etc/rstudio/rserver.conf
```

    #cb4-1www-allow-origin=mysubdomain.mydomain.com
    #cb4-2www-allow-origin=*.mydomain2.com
Note that wildcards (`*`) are accepted and will match any character, including hostname separators. For instance, the previous example of `*.mydomain2.com` will match both `foo.bar.mydomain2.com` and `foo.mydomain2.com`.
Back to top
