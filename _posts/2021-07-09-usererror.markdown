---
layout: post
title:  "User Error"
date:   2021-07-08 10:00:00 -0600
categories: plumbing
---
Like many, I've spent much of the past year working from home. To improve my productivity, I created a wired and wireless infrastructure in my house. My internet service provider is Verizon FiOS, and their service is excellent, with good bandwidth and excellent reliability. 

The following diagram summarizes the overall network configuration:

![default](/images/netconfig.png)

The "consumer" network is managed by a Google Mesh WiFi system. This mesh router is connected to the FiOS router through a wired connection. The mesh router is also connected to an Ethernet switch, and this switch provides wired connection for consumer devices including a game console, media streaming device, and a Chromecast-equipped smart TV.

The "professional" network is managed by the FiOS router. An Ethernet switch is connected to the router, and it provides wired connections for a Linux host (used to serve a hobby web site), a laser printer, the wired dock for my work computer, and a second media streaming device.

The laser printer and the Linux host are both configured with static IP addresses, and the FiOS router is configured to forward ports 80 and 443, along with a nonstandard port for SSH traffic, to that device. 

The FiOS router is also configured to provide a second wireless network. This network is for "backup" in case of problems with the mesh network.

Earlier this year, the network stopped working, with the exception of the backup wireless network. The mesh router couldn't see the FiOS router, and noe of the wired devices could see the router either.

Looking at the status page for the FiOs router, it showed the two wired connections were active (the mesh router and the switch), but the overall interface was shown as "Disconnected":

![default](/images/wheresmyethernet.png)

I couldn't find any information about this specific error, so I finally concluded that there was a hardware problem with the FiOS router and obtained a replacement, which fixed the problem.

Then the problem happened again. Verizon support asked me to verify that the router's wired ports were not working by directly connecting a PC. I did so, and the PC could not connect ("no network detected").

What I didn't do, however, was disconnect the other devices from the router before performing the test. I decided to perform a hard reset of the FiOS router and reconstruct the network from scratch. When I connected the mesh router, everything was working fine. When I connected the switch, everything stopped working.

The underlying problem turned out to be that I had not configured the FiOS router's IPV4 address distribution settings. As soon as I excluded the static IP addresses from the IPV4 address distribution, the problem was solved. 

I'm not sure exactly what led me to the solution, althoug as soon as I realized that the wired Ethernet switch was somehow poisoning the FiOS router, I had a good clue. I was somewhat surprised that the presence of a misconfigured device would take down the entire wired side of the router.
