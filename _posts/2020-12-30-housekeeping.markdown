---
layout: post
title:  "Free hosting (multiple domains, SSL, subdomains)"
date:   2020-12-30 14:30:55 -0500
categories: plumbing
---
In addition to this domain, I had a couple others languishing at the somewhat [skeevy godaddy.com](https://gizmodo.com/godaddy-sorry-we-promised-holiday-bonuses-that-was-ju-1845948766). I'd configured them to redirect to a pair of Medium publications, back when Medium was offering [free SSL certificates for custom domains on their site](https://help.medium.com/hc/en-us/articles/115003053487-Custom-Domains-service-deprecation). 

I also had an obsolete Lenovo laptop running Linux, with Verizon Fios 
internet service, pointed to by a a DDNS name from noip.com I'd been using it for hobby-grade stuff, but the lack of HTTPS was going to be a problem.

But thanks to this article from [Jeremy Gale](https://medium.com/@jeremygale/how-to-set-up-a-free-dynamic-hostname-with-ssl-cert-using-google-domains-58929fdfbb7a)
I was able to redirect my two domains to my on-prem Ubuntu server, set up a number of subdomains, and obtain free SSL certificates for everything. 

Here's a short recap of what I did.
<!--more-->
# Domain transfer from Godaddy to Google Domains

At <https://domains.google.com> you can easily initiate a domain transfer. The site walks you through the process, transfers any custom DNS settings, and credits
you for remaining time from godaddy.com. Cost is $12 per domain for the transfer, and $12 for renewals thereafter.

## Configure DDNS

Within the **DNS Settings**, create a **Synthetic Record** of type **DDNS**. Don't bother setting the IP address -- that is set by
this script, run from the host, that uses [Google Domains API](https://support.google.com/domains/answer/6147083?hl=en).

```
### Google Domains provides an API to update a DNS
### "Synthetic record". This script updates a record with 
### the script-runner's public IP address, as resolved using a DNS
### lookup.
###
### Google Dynamic DNS: https://support.google.com/domains/answer/6147083
### Synthetic Records: https://support.google.com/domains/answer/6069273

SQUAWK_USERNAME="****"
SQUAWK_PASSWORD="****"
SQUAWK_HOSTNAME="@.mistersquawk.com"

# Resolve current public IP
IP=$( dig +short myip.opendns.com @resolver1.opendns.com )
# Update Google DNS Record
URL="https://${SQUAWK_USERNAME}:${SQUAWK_PASSWORD}@domains.google.com/nic/update?hostname=${SQUAWK_HOSTNAME}&myip=${IP}"
curl -s $URL
```

## Set up subdomains

I'm interested in a few subdomains for various projects -- you can easily set those up by adding **Custom resource records** of
type **CNAME**:
 
![CNAMES](/images/CNAMES.png)

For example, after working through the [Angular Tour of Heroes](https://angular.io/tutorial) project, I decided to deploy it to
<https://heroes.mistersquawk.com>.

# Configure nginx server block for each domain

Because I had two domains, but only one host, I was pleased to find this post on 
[configuring multiple domains with Nginx on Ubuntu](https://www.serverlab.ca/tutorials/linux/web-servers-linux/how-to-configure-multiple-domains-with-nginx-on-ubuntu/). 

The short story is you simply create a server directive block for each domain or subdomain, specifying the location of the files to serve.

```
server {
     listen 80;
     listen [::]:80;
     server_name domain-one.com www.domain-one.com;

     root /var/www/domain-one.com/public_html;

     index index.html index.htm;

     location / {
          try_files $uri $uri/ =404;
     }
}
```

# Use certbot and LetsEncrypt to generate and install certificates

Certificates? That turned out to be the easiest of all. Simply head to https://certbot.eff.org/ and follow the instructions. 

```
sudo certbot --nginx
```

Automatically analyzes your Nginx configuration and requests, then installs the reqired certificate. Once you've verified that it works

```
$ certbot renew --dry-run
```

Add this to your crontab to auto-renew the certificates:

```
18 4 * * * /usr/local/bin/certbot renew
```

