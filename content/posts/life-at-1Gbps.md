---
date: '2026-01-04T16:16:14-06:00'
draft: false
title: 'Life at 1Gbps'
tags: ["internet", "speed"]
---
Last summer we got 1Gbps internet access when [fiber finally came to our curb](https://en.wikipedia.org/wiki/Fiber_to_the_x).

Symmetric 1Gbps down/up is a significant upgrade from the old 400/10Mbps cable connection for about the same cost.

As a small test, I did the below download on my home linux box using [wcurl](https://curl.se/wcurl/).

This downloads a 773 MiB ISO image file from the local Cloudlfare POP in MSP.

Here the file was already cached at Cloudflare.  It downloads at 85.6 MiB per second over a single HTTP/2 TCP connection, this is finished in 9 seconds:

```
$ wcurl --curl-options -v -O - https://cloudflare.cdn.openbsd.org/pub/OpenBSD/7.8/amd64/install78.iso  > /dev/null

< HTTP/2 200
< content-length: 811106304
< server: cloudflare
< cf-cache-status: HIT
< cf-ray: 9b8e1ca62f05a1fa-MSP

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  773M  100  773M    0     0  85.6M      0  0:00:09  0:00:09 --:--:-- 90.9M
```

If we ask GenAI to do the math for very old modem times for the least-slow [56k modems](https://en.wikipedia.org/wiki/Modem#56k) that existed it is 32-34 hours:

```
How long to download 773MiB file on 56k modem?

56k modem speed
Advertised: 56 kbps (56,000 bits/sec)
Real-world max is usually ~53.3 kbps, often a bit less due to line quality and overhead

At 56 kbps:
6,480,000,000 ÷ 56,000 ≈ 115,700 seconds ≈ 32.1 hours

At 53.3 kbps (more realistic):
≈ 33.8 hours
```

Remember the old days of going to physical stores to buy music and software CDs/DVDs?