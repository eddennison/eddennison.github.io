---
layout: post
title:  "Google Domains, We Hardly Knew Ya"
date:   2024-03-02 15:30:55 -0500
categories: plumbing
---
Three years ago I [wrote about]({% post_url 2020-12-30-housekeeping %}) deploying a pair of sites using HTTP from a $100 fanless PC over my FIOS connection. The post details how [Google Domains](https://en.wikipedia.org/wiki/Google_Domains) can be used to implement a roll-your-own DDNS system for domains registered there. 

Not long after [officially announcing that Google Domains](https://blog.google/outreach-initiatives/entrepreneurs/register-a-domain-google-domains/) was out of beta on March 15, 2022, Google [sold Google Domains to Squarespace](https://support.google.com/domains/answer/13689670?hl=en). I had been hoping that the API would continue to be supported by Squarespace, but sadly no.

![CNAMES](/images/gd_domains.png)

Based on a quick Google search, it looked like [Cloudfare](https://www.cloudflare.com/) was a viable replacement. Primarily known as a CDN-as-a-service provider, Cloudflare also offers domain registry services and an API that allows you to edit DNS records.

The domain migration process was trivially easy (my domains had not yet been migrated to Squarespace), the domain registration is cheaper, and the API straightforward to use. Some configuration details follow.
<!--more-->

## DNS Records: Disable Proxying

As part of their CDN, Cloudflare proxys DNS requests. For simplicity, disable this setting for all of your A and CNAME records (unless you want to update your NGINX configuration).

## Scripted DDNS Updates

Based on [this example](https://gist.github.com/Tras2/cba88201b17d765ec065ccbedfb16d9a), I created a script to update the IP address in the A records for both domians as needed.

(Since retiring, I haven't done much computer work, so please forgive the many flaws below.)
```bash
### !/bin/bash
#
# Use CloudFlare API to update A record with new IP address when needed.
#
SQUAWK_ZONE="xxx.com"
HA_ZONE="xxx.com"

AUTH_KEY="xxx"
AUTH_EMAIL="xxx.y.com"

SQUAWK_ZONE_ID=xxx
HA_ZONE_ID=xxx

UPDATE_TIME=$(date)

# Resolve current public IP

IP=$( dig +short myip.opendns.com @resolver1.opendns.com )

# For each virtual host, check to see if the DNS record matches our IP address

if host $SQUAWK_ZONE 1.1.1.1 | grep "has address" | grep "$IP"; then
  echo "$SQUAWK_ZONE is currently set to $ip; no changes needed"
  UPDATE_SQUAWK=false
fi
if host $HA_ZONE 1.1.1.1 | grep "has address" | grep "$IP"; then
  echo "$HA_ZONE is currently set to $ip; no changes needed"
  UPDATE_HA=false
fi

# If a host needs updating,first get the record ID for the A-record
# then use PATCH to udpate the value. For details see the excellent
# Cloudflare documntation: https://developers.cloudflare.com/api/operations/dns-records-for-a-zone-patch-dns-record

if $UPDATE_SQUAWK ; then
    dnsrecordid=$(curl -s -X GET "https://api.cloudflare.com/client/v4/zones/$SQUAWK_ZONE_ID/dns_records?type=A&name=$SQUAWK_ZONE" \
    -H "X-Auth-Email: $AUTH_EMAIL" \
    -H "X-Auth-Key: $AUTH_KEY" \
    -H "Content-Type: application/json"  | jq -r '{"result"}[] | .[0] | .id')

    # echo "DNSrecordid for $SQUAWK_ZONE is $dnsrecordid"

    curl --request PATCH \
    --url https://api.cloudflare.com/client/v4/zones/$SQUAWK_ZONE_ID/dns_records/$dnsrecordid \
        --header  "X-Auth-Email: $AUTH_EMAIL" \
        --header  "X-Auth-Key: $AUTH_KEY" \
    --data '{
    "content": "'$IP'",
    "name": "'$SQUAWK_ZONE'",
    "proxied": false,
    "type": "A",
    "comment": "'"DDNS Updated on $UPDATE_TIME"'",
    "ttl": 1
    }'
fi
if $UPDATE_HA ; then
    dnsrecordid=$(curl -s -X GET "https://api.cloudflare.com/client/v4/zones/$HA_ZONE_ID/dns_records?type=A&name=$HA_ZONE" \
    -H "X-Auth-Email: $AUTH_EMAIL" \
    -H "X-Auth-Key: $AUTH_KEY" \
    -H "Content-Type: application/json"  | jq -r '{"result"}[] | .[0] | .id')

    # echo "DNSrecordid for $HA_ZONE is $dnsrecordid"

    curl --request PATCH \
    --url https://api.cloudflare.com/client/v4/zones/$HA_ZONE_ID/dns_records/$dnsrecordid \
        --header  "X-Auth-Email: $AUTH_EMAIL" \
        --header  "X-Auth-Key: $AUTH_KEY" \
    --data '{
    "content": "'$IP'",
    "name": "'$HA_ZONE'",
    "proxied": false,
    "type": "A",
    "comment": "'"DDNS Updated on $UPDATE_TIME"'",
    "ttl": 1
    }'
fi
```
For extra credit, the script updates the comment in the A record to indicate when it was updated:

![CNAMES](/images/cf_update_date.png)
