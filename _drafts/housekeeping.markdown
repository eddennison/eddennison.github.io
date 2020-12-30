---
layout: post
title:  "Free hosting (multiple domains, SSL, subdomains)"
date:   2020-02-2 14:30:55 -0500
categories: plumbing
---
In addition to this domain, I had a couple others languishing at godaddy. I'd configured them to redirect to a pair of Medium publications, back
when Medium was offering free SSL certificates for custom domains on their site. I also had an obsolete Lenovo laptop running Linux, with Verizon Fios 
internet service.

I'd been using noip.com to establish a DDNS name for my server, and I've been using it for hobby-grade stuff, but the lack of HTTPS was going to be a problem.

But thanks to a collection of great tips from https://medium.com/@jeremygale/how-to-set-up-a-free-dynamic-hostname-with-ssl-cert-using-google-domains-58929fdfbb7a, 

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


