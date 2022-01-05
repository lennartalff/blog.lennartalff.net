---
layout: single
excerpt: Using multiple hostnames on the same IP address with Apache2 virtual hosts.
title:  "Adding Apache2 Virtual Hosts"
date:   2021-11-25
last_modified_at: 2022-01-05T12:54:26
categories: Software
header:
    overlay_image: /assets/images/default_splash.png
---

## Why?

I have a domain and want to use different services with different subdomains on a single server. So for example I have a already running Nextcloud instance and want to add this Jekyll powered website.

## Status Quo

SSL certificates for my websites are provided by [Let's Encrypt](https://letsencrypt.org) and installed and updated by `certbot`. I have set up a `cron` job to renew my certificates regularly

{% highlight sh %}
certbot renew --rsa-key-size 4096 --renew-hook "systemctl restart apache2"
{% endhighlight %}

To check for which domains I have certificates, I can run
{% highlight sh %}
sudo certbot certificates
{% endhighlight %}

I will need these information later when I add a domain for an existing certificate.

## Solution