---
date: '2025-04-07T12:00:14-05:00'
draft: false
title: '6 Milliseconds From Facebook'
tags: ["ntp", "latency"]
---
I use [chrony](https://chrony-project.org) to keep my home linux server synced with public NTP servers.

It's fun to watch which servers have the lowest latency and estimated error.

For me the lowest latency is [Facebook's Public NTP](https://engineering.fb.com/2020/03/18/production-engineering/ntp-service/).

It's easy to think `time3.facebook.com` has the highest estimated error.  But no the unit there is us or μs meaning microseconds, so this is 6.4 milliseconds.

All this on the cheapest cable internet connection available. :)

```bash
$ chronyc sources -v

  .-- Source mode  '^' = server, '=' = peer, '#' = local clock.
 / .- Source state '*' = current best, '+' = combined, '-' = not combined,
| /             'x' = may be in error, '~' = too variable, '?' = unusable.
||                                                 .- xxxx [ yyyy ] +/- zzzz
||      Reachability register (octal) -.           |  xxxx = adjusted offset,
||      Log2(Polling interval) --.      |          |  yyyy = measured offset,
||                                \     |          |  zzzz = estimated error.
||                                 |    |           \
MS Name/IP address         Stratum Poll Reach LastRx Last sample
===============================================================================
^- time1.facebook.com            1  10   377   806  -1083us[-1059us] +/-   34ms
^- time2.facebook.com            1  10   377   197  +8361us[+8385us] +/-   76ms
^* time3.facebook.com            1  10   377    83   -708us[ -683us] +/- 6466us
^- time4.facebook.com            1  10   377    40  +4300us[+4300us] +/-   25ms
^- time5.facebook.com            1  10   377   610   -918us[ -894us] +/-   19ms


$ chronyc -n sources -v

  .-- Source mode  '^' = server, '=' = peer, '#' = local clock.
 / .- Source state '*' = current best, '+' = combined, '-' = not combined,
| /             'x' = may be in error, '~' = too variable, '?' = unusable.
||                                                 .- xxxx [ yyyy ] +/- zzzz
||      Reachability register (octal) -.           |  xxxx = adjusted offset,
||      Log2(Polling interval) --.      |          |  yyyy = measured offset,
||                                \     |          |  zzzz = estimated error.
||                                 |    |           \
MS Name/IP address         Stratum Poll Reach LastRx Last sample
===============================================================================
^- 2a03:2880:ff0b::123           1  10   377   808  -1083us[-1059us] +/-   34ms
^- 2a03:2880:ff0c::123           1  10   377   199  +8361us[+8385us] +/-   76ms
^* 2a03:2880:ff08::123           1  10   377    85   -708us[ -683us] +/- 6466us
^- 2a03:2880:ff09::123           1  10   377    42  +4300us[+4300us] +/-   25ms
^- 2a03:2880:ff0a::123           1  10   377   612   -918us[ -894us] +/-   19ms
```