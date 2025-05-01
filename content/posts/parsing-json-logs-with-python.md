---
date: '2025-04-19T10:35:04-05:00'
draft: false
title: 'Parsing JSON Logs With Python'
tags: ["caddy", "json", "structured logs", "python"]
---
It's 2025 and you find yourself using [Caddy](http://caddyserver.com) as the home webserver.

One nice feature of Caddy is [structured logging](https://caddyserver.com/docs/logging#structured-logs).  

Caddy's access logs include **all** request/response headers and fields as newline-delimited JSON.  This is a bit mind-blowing coming from something like nginx or Apache's [Common Log Format](https://en.wikipedia.org/wiki/Common_Log_Format) from 3 decades ago.

Structured logs are great when loaded into ELK to automatically parse and index fields.

But what to do at home where there is no ELK, and I want to know simple things like:
- What is the date/time for the log?
- What countries/Cloudflare POPs are requests coming from?
- What URLs are bots hitting most?
- Are there unexpected errors?

How to parse the wall of JSON documents?

[jq](https://jqlang.org) is a great for pretty printing or extracting a single field from a JSON document.  But I find the syntax hard to understand and remember especially for extracting multiple fields.

We can do this with a few lines of Python.  These particular batteries are included in reasonably-modern python3, no extra packages needed:

```python
#!/usr/bin/env python3

import datetime
import json
import sys

for line in sys.stdin:
    data = json.loads(line)
    localTS = datetime.datetime.fromtimestamp(data['ts'])
    host = data['request']['host']
    uri = data['request']['uri']

    requestHeaders = data['request']['headers']
    cfRay = requestHeaders.get('Cf-Ray')
    cfIpCountry = requestHeaders.get('Cf-Ipcountry')

    status = data['status']

    responseHeaders = data['resp_headers']
    cacheControl = responseHeaders.get('Cache-Control')

    print(f'localTS={localTS} host={host} cfRay={cfRay} cfIpCountry={cfIpCountry} status={status} cacheControl={cacheControl} uri={uri}')
```

Above reads and parses all lines from stdin so works nicely in a shell pipeline.  The output is easy to parse with grep/awk/sed, etc.

Below is an example of spotting a bad guy looking for wordpress sites:

```sh
$ tail -1000 access.log | parse_logs.py | grep 'status=404' | grep wp

localTS=2025-04-20 07:04:04.185519 host=aaronr.digital cfRay=['93348085ad18f826-MAN'] cfIpCountry=['IE'] status=404 cacheControl=['public, max-age=60'] uri=/wp_class_datalib.php
localTS=2025-04-20 07:04:04.701258 host=aaronr.digital cfRay=['933480878df7f826-MAN'] cfIpCountry=['IE'] status=404 cacheControl=['public, max-age=60'] uri=/wp_wrong_datlib.php
```