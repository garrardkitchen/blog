---
title: "The Grass Is Rarely Greener"
date: 2022-01-30T18:27:54Z
tags: azure, aws, promises, relationship
---

I had a conversation recently and this reminded me of something that happened to me.  Hence, why I'm writing this Post.  The conversation was over moving to a different Cloud provider. Hopefully, this Post's title makes sense now!

Several years back, the company that I was working for at the time shifted to AWS, away from Azure.  It was heavily suggested - I was present during that conversation - that it would be in our favour if we "jumped ship" to _their_ platform.  "Their" being "AWS".  Long story short, sadly it was not.  In fact, it turned out to be a huge waste of time.  IMO, they should never have suggested this.  There were other suggestions made that _they_ really should not have made; sadly we followed these too.  In retrospect, it was totally unjustifiable not to mention unfair for them to suggestion any of this for a company of that size [small], especially knowing how long all of this would take.  I even gained 2 certifications to help best advice the company on how to implement/integrate with the AWS Platform and backing services.

If you are thinking, "You are grown ups with years of experience under your belt yeah, so you didn't have to go with what they said?!".  Indeed, but it was AWS and these people we were talking to were big hitters and as such, the "carrot" was big. Who wouldn't right?!

I will return to this in a later post but for now I will callout, IMO, several important and possibly over-looked considerations when contemplating a move to a different Cloud Provider.

# Considerations when moving to another Cloud Provider

Here are a few nuggets that will hopefully resonate:

1 - I don't know of any business that will allow their "technical people" to have their way and purely focus on tech debt or similar, without developing features (or improvements) over a prolonged amount of time.  With all the best intentions in the world, migrations are time consuming (not to mention a scheduling nightmere), and this time is exponential when dealing with new or unfamiliar tech.  Tech will still present problems, wherever it is.

2 - Cloud providers offer essentially the same backing services - orchestration, serverless compute, VM compute, db, caching, temporal decoupling thru queues, etc.... They may make it easier or more attractive for particular language techs but essentially, it's the same offerings and concepts (EIP but in the Cloud) but elsewhere. Not to mention they all have their own version of the 5 pillars of well-architecture framework.

3 - As an extension of (2) above, there's training to consider.  Also, new problems to solve, and migration paths to figure out.  Not to mention RBAC and ownership.  Oh yeah, don't forget DevOps, possibly made slightly easier if you've opted to a non-native IaC approach like TF.  Oh yeah, don't forget automation, runbooks, alerting, 3 pillars of observation (incl. distributed tracing). Oh yeah, ...

4 - If there's something that your current cloud provider doesn't offer, you can also look for a SaaS solution alternative.  All you are really interested in are specific use-cases/features, integration options (SDKs) and it being a managed service as as to avoid requiring additional Engineers to manage it and perform associated activities to ensure it's HA and secure.  

5 - It's always going to take longer than you think.  Fact.  We tend to think "happy path".

6 - Now this next one is a biggy ... if you're moving to new tech, cloud platform, language or different backing services you're going to hemorrhage staff.  Best guess, at least 50%.  And with that, domain knowing 😱.  It's never nice to see people go, morale takes a hit big time.  Then there's recruitment, planning and onboarding.

7 - If cost is a consideration or motivation for the move then what are you doing re: the Cost Optimisation tenet of your provider's flavour of a "Well Architected" Framework?  More important than this but less obvious, what is the Cloud Service Provider (CSP) or organisation that is the conduit between you and the Cloud Vendor doing to help?  In my experience, they only get involved if you need help with an issue, or if there's a zero day exploit you need to be made away of or other type of issue involving one of your backing services.  Anything difficult, then they tend to hand off to the Cloud Vendor themselves, but hey, at least they're on the same support call with you! 👀.  They generally don't get in your face either if you're spending a futune!  You may get the occasional email from them letting you know that you are very important to them.  IMO, they need to be continually proving their worth to you [your organisation] and if they're not, engage a different CSP  or employ certified professional(s) who can evaluate your architecture, and your DevOps processes as well as costs.  Essentially, you need certified/experienced Engineers to help to make real tangible contributions and improvements.

8 - If technical issues are plaguing you, then likely they'll manifest themselves also on a different cloud provider.  So, if you've invested heavily with a particular tech, or approach, and this is too costly, too risky to the business due to instability, first understand why first before jumping ship.  You never know, you might be using it ineffeciently or incorrectly.  Take AKS for instance.  It's hugely involved.  If you get any part of that wrong, it's a risk to your business.

There will be more I'm sure, but for the sake of getting closure on this topic, I will leave it at this.  

# Recommendations

In summary, my advice is this; evaluate your current platform to identify (in no particular order):

- Cost savings (eg there could be better bin packing or scaling options or up to 70% reduction on "compute" by using Reserved Instances)
- Configuration changes to improve stability, minimize failure on upgrading your dependencies, and self-healing
- Improvements on patterns and practices used by your Engineers (incl. low code options, delegate failure edge/corner cases to your architecture, consistency of solution - don't solve the same problem multiple ways!, only measure what you can action)
- Automation (eg runbook when metric exceeded, cron jobs)
- What is your prominate tech?  For example, if your ecosystem is predominately .NET then Azure is a justifiable Cloud Platform.  You simply won't get the same depth/scope of features/integrations if you go with a different Cloud Provider.  Think about it...would you offer up all your crown jewels to a competing provider? I rest my case!

