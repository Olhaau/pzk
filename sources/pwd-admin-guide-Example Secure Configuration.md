---
title: Example Secure Configuration – Administration Guide
keywords: source
---

# Example Secure Configuration – Administration Guide

url:: https://docs.posit.co/ide/server-pro/hardening/example_secure_configuration.html
up: [[sources]]

This section aggregates all of the security recommendations from the previous sections. Note, again, that some adjustment is likely to be necessary depending on your environment; for example, this set of configuration values presumes that SSL termination is happening in Posit Workbench, that Workbench is the only application running on its domain, and that it is never embedded in another page.

Therefore, use these files as a starting point rather than copying and pasting them into your own system.  

```
/etc/rstudio/rsession.conf
```

    #cb1-1# Disable publishing to RPubs and shinyapps.io
    #cb1-2allow-external-publish=0
    #cb1-3
    #cb1-4# Prevent exploration of system directories
    #cb1-5restrict-directory-view=1
```
/etc/rstudio/rserver.conf
```

    #cb2-1# Limit access to those users to whom it's been explicitly granted via group membership
    #cb2-2auth-required-user-group=rstudio-users
    #cb2-3
    #cb2-4# Sign users out after 20 minutes of inactivity (default is 60)
    #cb2-5auth-timeout-minutes=20
    #cb2-6
    #cb2-7# Use HTTPS when connecting to web browsers
    #cb2-8ssl-enabled=1
    #cb2-9ssl-certificate=/var/certs/your_domain_name.crt
    #cb2-10ssl-certificate-key=/var/certs/your_domain_name.key  
    #cb2-11
    #cb2-12# Limit SSL protocol versions to modern TLS
    #cb2-13ssl-protocols=TLSv1.2 TLSv1.3
    #cb2-14
    #cb2-15# Increase HTTP Strict Transport Security to 1 year and include subdomains
    #cb2-16ssl-hsts-max-age=31536000
    #cb2-17ssl-hsts-include-subdomains=1
    #cb2-18
    #cb2-19# Enable origin checks on all HTTP requests (CSRF defense)
    #cb2-20www-enable-origin-check=1
    #cb2-21
    #cb2-22# Ensure that the domain on which RStudio is hosted is permitted as an origin
    #cb2-23www-allow-origin=mysubdomain.mydomain.com
    #cb2-24
    #cb2-25# Ensure the SameSite attribute is set on all cookies
    #cb2-26www-same-site=lax
    #cb2-27
    #cb2-28# Disallow embedding on other pages
    #cb2-29www-frame-origin=none
    #cb2-30
    #cb2-31# Use HTTPS when connecting to the Job Launcher
    #cb2-32launcher-use-ssl=1
```
/etc/rstudio/launcher.conf
```

    #cb3-1enable-ssl=1
    #cb3-2certificate-file=/var/certs/your_domain_name.crt
    #cb3-3certificate-key-file=/var/certs/your_domain_name.key  
```
/etc/rstudio/database.conf
```

    #cb4-1# Configure SSL certificate authentication with postgres
    #cb4-2connection-uri=postgresql://postgresuser@mysubdomain.mydomain.internal:5432/rstudio?sslcert=/etc/ssl/certs/postgresql/postgresql.crt&sslkey=/etc/ssl/certs/cacert/postgresql/postgresql.key&sslrootcert=/etc/ssl/certs/cacert/postgresql/root.crt
Back to top
