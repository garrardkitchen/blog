---
title: "Runtimes"
date: 2022-01-08T11:09:12Z
tags: .net framework, .net core, .NET 6.0, .NET 3.1, end-fo-life support, linux, windows, syntax, self-hosted runner, cicd, microsoft fasttrak, code quality, image scanning, licensing, cve, nuget, npm
---

In my current role as Head of Cloud Platform, I am leading the technical effort of migrating our entire on-premise real-estate to Azure.  Part of this mission, is to upgrade the runtimes of our applications, regardless of their current placement; IIS Web apps, Windows Services and Docker Swarm containers.  I say "part of this mission" as another aspect of this migration is to create a new foundation for our platform - AKS.  I hope to cover more on this in later posts.

With being deliberately ambiguous on actual numbers here, we have a _fair_ amount of workloads that are running on runtimes that have surpassed their end-of-life support status 😱.  I am sure we're not the only organization that finds itself in this predicament.  One of the first things our CTO wanted when he came onboard was for us to sort out our runtimes.

What this means is that we have .NET Framework and .NET Core workloads that need to be migrated to a runtime that is not out of support (now or anytime soon).  At the same time, the target runtime has to have hit the mature (at least on it's 1st minor release) status.  .NET 6.0 is now GA but it's paint is still a little wet.  Understandably there is some concern, borderline trepidation towards targeting this new version.  There are impactful benefits (improved performance, greater stability, improved GC, cost benefits resulting from being more perfomant, less vulnerabilities, improved security, etc...) and our current runtime target for our .NET Core workloads is 3.1.  However, end-of-life support for this particular version is the end of this year - [03.Dec.2022](https://docs.microsoft.com/en-us/lifecycle/products/microsoft-net-and-net-core).  So, at some point, this upgrade needs to happen.  With planning and prioritization, this can happen when appropriate.

One strategy of delivering confidence with a runtime is to update a few apps, consecutively; providing you've availability to accommodate such an approach.  Timeboxed: (1) A simple (CRUD-esque HTTP API that sits on top of a Db) workload then (2) one that involves an `data-in-motion` aspect (message queue, consumer paradigm, non-http api).  

Another approach to reducing concern, mitigating risk, etc... is to meet with the SME (Subject matter expert) - a Microsoft representative - and listen to what they have to say.  I have orchestrated such a meeting - we are being helped/guided by Microsoft FastTrak.  The advice received was to upgrade.  We were also reminded of the migration paths to take when moving to a target version.  Here are some demonstrative links that will help you through this process:

- Migration guidance found here ➡ [migrate from asp.net core 3.1 to 6.0](https://docs.microsoft.com/en-us/aspnet/core/migration/31-to-60?view=aspnetcore-6.0&tabs=visual-studio).  

- An example of breaking changes between versions can be found here ➡ [breaking changes in .NET Core 3.1](https://docs.microsoft.com/en-us/dotnet/core/compatibility/3.1)

Most of our workloads are simple HTTP API CRUDs that sit aloft databases.  In short, we're not doing anything too complicated and so this makes most of our pain points the result of external NuGet package dependencies.

IMO, it is important to establish, if at all possible, a feedback loop back to Microsoft.  If you are fortunate to have an established relationship with Microsoft and have access to certain areas/teams - eg FastTrak, or even a Support plan, if/when issues do arise (eg increased exception frequency, uncharacteristic high latency, poor performance, frequent CG, memory leaks, socket exhaustion, etc...), you can feed these back and receive remediation advice until a fix (if not a result of poor implementation) becomes available.  We had been plagued with issues (socket exhaustion, CG, memory leaks) with those workloads on ASP.NET Core 2.* but now see less issues with those ASP.NET Core workloads that are now on 3.1.

To complement the above I would also recommend agreeing on, and capturing, metrics that are demonstrative of your gains from the runtime upgrade.  This is where metrics come into their own. Generally, metrics tend to have a greater retention period than logs, and therefore can paint an informed picture over a wider span of time than logging can.  Generally speaking, lack of memory is going to be more catastrophic than thottling through lack of CPU.  When your RSS or working set bytes are depleted, more often than not your application will terminate due to OOM (out of memory) exception - state and workflow blown away unless you've employed patterns to mitigate against these edge/corner cases - most don't.  In particular, I am referring to container_memory_rss and container_memory_working_set_bytes due to working with kubernetes.

The above is not a definitive list of must do's and in part, is overly simplistic. IMO, it is important to stay current with runtimes to benefit from the improvements they deliver on.  For me though, their stability and the vulnerabilities they address is the most important.  It is scary how many organizations run on runtimes that are out of support.  Applications will still run but ask yourself this, how much is this truly costing your company, how much is it costing our environment and finally, how much could your company suffer because they are exposed to vulnerabilities?

Sounds overly dramatic doesn't?!  I stop there as I don't wish to perpetuate the negativity of going down this particular hole.  But before I do; more food for thought.  I would like to throw into the mix application dependencies and in particular those package dependencies some of us rely on - NuGet & npm.  Good DevOps practices mitigate again licensing or vulnerability scanning but IMO the same level of focus ought to be concentrated into this area as well.  This was painfully brought to light recently regarding OSS and the log4j vulnerability - CVE-2021-44228 (common vulnerabilities and exposure). And finally, docker image scanning.  There are plenty of solutions out there - Snyk for example - that can help with this.  Good docker practices go a long way also toward protecting us against vulnerabilities that arise from images and not forgetting code scanning products generally that identify smelly code, bugs and other vulnerabilities.  I have introduced solutions that deal with all of this.