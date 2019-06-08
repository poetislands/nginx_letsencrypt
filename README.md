nginx_letsencrypt
=================

The nginx_letsencrypt role provides a basic configuration for an Nginx site over https.

Requirements
------------

The role has been tested with CentOS 7 and Debian 9.

Role Variables and usage
--------------

The default variables for this role are in `defaults/main.yml`. You should override these per host as required.

For each host you should also specify nginx.ip for the ip on which Nginx should listen and nginx.url for the url of the site.

By default the role requests a staging certificate, which is an invalid certificate for testing purposes. This is to prevent users from accidently requesting too many certificates for a host and thus hitting the Letsencrypt rate limit of 5 per week. To request a valid certificate for a host override the default by setting letsencrypt_staging to no for that host.

You should define the upstreams and locations yourself. The server definition includes locations via:
```
include /etc/nginx/conf.d/{{ nginx.url }}_upstream*;

server {
...
    include /etc/nginx/conf.d/{{ nginx.url }}_location*;
}
```
So templating your upstream and location definitions and naming them in accordance with the include statements is enough.

Example usage
----------------

Define a host and make it part of the group servers.
Use in its host_vars:
```
---
nginx:
  listen_ip: 192.168.30.100 
  url: example.com
letsencrypt_staging: no
letsencrypt_mail: letsencrypt@example.com
```

And in its playbook:
```
---
- hosts: servers
  roles:
    - nginx_letsencrypt
```
And add location definitions.