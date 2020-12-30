---
layout: post
title:  "Free hosting (multiple domains, SSL, subdomains)"
date:   2020-02-2 14:30:55 -0500
categories: plumbing
---
In addition to this domain, I had a couple others languishing at the somewhat skeevy 
[godaddy.com](https://gizmodo.com/godaddy-sorry-we-promised-holiday-bonuses-that-was-ju-1845948766). I'd configured them to redirect to a pair of Medium publications, back
when Medium was 
offering [free SSL certificates for custom domains on their site](https://help.medium.com/hc/en-us/articles/115003053487-Custom-Domains-service-deprecation). 

I also had an obsolete Lenovo laptop running Linux, with Verizon Fios 
internet service, pointed to by a a DDNS name from noip.com I'd been using it for hobby-grade stuff, but the lack of HTTPS was going to be a problem.

But thanks to a collection of great tips from https://medium.com/@jeremygale/how-to-set-up-a-free-dynamic-hostname-with-ssl-cert-using-google-domains-58929fdfbb7a 
I was able to redirect my two domains to my server, set up a number of subdomains, and obtain free SSL certificates for everything. Here's a short recap of 
what I did.

# Domain transfer from Godaddy to Goole Domains

At https://domains.google.com you can easily initiate a domain transfer. The site walks you through the process, transfers any custom DNS settings, and credits
you for remaining time from godaddy.com. Cost is $12 per domain.

## Configure DDNS

Within the **DNS Settings**, create a **Synthetic Record** of type **DDNS**. Don't bother setting the IP address -- we'll 
use the following script, run from the host, to do that using [Google Domains API](https://support.google.com/domains/answer/6147083?hl=en).

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

For example, after working through the [Angular Tour of Heroes](https://angular.io/tutorial) project, I decided to deply it to
[](https://heroes.mistersquawk.com).

# Configure nginx server block for each domain


# Use certbot and LetsEncrypt to generate and install certificates



In addition to this site's domain, I also own a couple of others. Years ago, I had a modestly popular "personal finance blog", and one of these domains 
pointed to that blog (on blogger). The other (mistersquawk.com) was acquired on a whim.

A few years ago, as it became clear that non-https sites were going to become digital pariahs, I redirected those domains to a pair of publications on medium.com.
I was full of ambitions to restart a blog using Medium, but nothing came of it.

In the mean time, I had installed Linux on a derelict Lenovo laptop and exposed it to the internet using port forwarding on my Fios router. I set up an Apache web
server, and I configured it for SSH publickey access. I also acquired a DDNS name from noip.com (free, but you had to renew it each month), so I could work with 
this system from anywhere (say at work). 

# Heading

Once you own a domain, especially domain named after yourself, you're kind of stuck with it. You can't really just throw it back into the ocean. For a long time,
this domain was pointed at a page created with the "original" Google Sites (you can see it [here](https://sites.google.com/site/eddennisonbeta/)), but there was no easy way to support https with a custom domain on legacy Google Sites. 

I was naturally pleased to discover how easy it was to get a Github-hosted Markdown site to work with https.


