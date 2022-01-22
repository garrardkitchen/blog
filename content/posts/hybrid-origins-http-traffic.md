---
title: "Hybrid Origins Http Traffic"
date: 2022-01-15T17:25:37Z
draft: true
tags: cloudflare, dns, uri forwarder, page rules, zones, domain, hostname
---

We're migrating our on-premise workloads to Azure.  This presents several challenges.  One of which is what I am covering specifically here in this post and that is ...

> How to reduce code changes effort?

This isn't about updating runtimes, this is about having workloads spread across different platforms that need to talk to each other (eg HTTP chaining).  It is not uncommon for one HTTP service to need to talk to another HTTP.  If you move one HTTP service to exist behind a different zone, you will need update references in those dependant service to point to the new DNS Hostname. If you've a few HTTP services then this really isn't an issue.  However, if you've 100+ services, with services a huge amount of HTTP dependencies, this quickly becomes daunting and a potential deployment scheduling nightmare.