---
title: "Reality of Engineering"
date: 2020-05-18T11:48:48+01:00
tags: ["good engineering", "good practices", "patterns", "architecture", "guiding principles"]
---

I gave myself some downtime this weekend.  The weather was rubbish so unfortunately I couldn't execute some "list" tasks.  Instead, I watched some movies, helped my boys find stuff, build and generally make a mess - ah, the good stuff yeah?!  This gave me the necessary space to think about some of the areas of engineering that would have benefited me from knowing "at the time".  Below is a list of guiding principles, advice and general experience in engineering resulting from this "moment of clarify".  I never know whether to call it engineering or development. Apologies if I switch between the two terms in this post.  I essentially mean the same thing.  One thing to note, it's a list with some content.  It not exhaustive and they are _just_ my opinions. As always, this is a reminder to myself but hoping this will help others too!

---

## No code is perfect

Put the keyboard down, and walk away....but only after you;ve finished writing the unit tests yes.  Code and requirements change frequently.  So, don't over think your code.  Just make sure you adhere to good engineering principles and it does exactly what it is required to do, no more.  Please don't add stuff that just isn't need as this will become somebody elses nighmare to support, refactor later!

Now, write tests and move on...after capturing tech debt of course!

## Tech debt

It happens all the time.  It's a just what happens when you develop. It shouldn't been seen as a bad thing, providing it is managed correctly and openly.  And that's one of the important things; to capture it.  So, what do you need to capture? Well, this is will give you a good start:
- what needs to be done to deal with it (e.g. re-platform to the cloud)
- why a decision has been taken to leave it as tech debt (e.g. temp solution to keep up momentum)
- what benefit it will give the organisation when dealt with (e.g. better resilience and `C`ustere`X`perience).

Simples.

## No man is an island

Keeping on the theme of Tech Debt; things like this need to be discussed openly.  More to the point, the inclusion of tech debt needs to be agreed AND it has to be captured.  This should be driven by good engineering leadership.

## Ideas; thick and fast

To work rapidly towards a solution, ideas need to be thick and fast.  These will be dismissed but well help shape the eventual solution.  This goes for both architecture and code solutionizing.  The quicker the ideas are out there, the quicker they can be discussed.  With these ideas being shared, the discussion n that will enevitably ensue will always bring the team members together.  `Collaboration` is your friend. The serendipity of this action will give more team members shared knowledge of this `problem domain` and will in part help in their development and education.

## Don't be precious of codebases

This time next month, 6 months, a year, 2 years, etc.. it won't look the same and most probably will not exists.

Write tests and move on.  

## TDD

This approach is always spoken of, especially in interviews or technical tests, but sadly not always adhered too in practice.  In my experience, it always, ALWAYS results in succinct, readable and less verbose code.  Oh yeah, with tests. Bingo!

## Kick towards the end goal

Quick releases will measure your success rate and offer the greatest amount of flexibility to react and adapt.  So what do I mean to react and adopt.  If you're developing a new feature.  You never, NEVER release a the all-encompassing, panacea.  You devise a MVP (minimum viable product, or feature), to purely test your original hypothesis.  This way, if no one needs it OR can't use it, you can rapidly either (a) chalk it up to experience and move on OR (b) improve the `Us`ere`X`perience.  Ah yes, slow development and big bangs don't help.  Whether it's a rewrite or a new feature, you won't know whether you're on the right track until it's out there and, here's the biggy, and being measured!  Yeah, I went there!  I mean, how else are you going to know whether your feature is working or being used right?  This includes re-engineering technical problems too.

## If developing features, have POs provide test-cases

There's 2 types of development work.  These are (a) Technical improves which address engineering improvements and (b) Features.  As engineers we'll know how to test our sh*t, but not necessarily how best to test a new feature.  this required excellent product domain knowledge  So, an important part of the effort is to have your POs (`P`roduct`O`wners) provide the test-cases.  This needs to be part of the Sprint.

## Let your architecture deal with resilience

A definition of Resilience:

    Resilience is the ability for your _solution) to continue to operate without issue, when faced with a transient failure.

Don't code for resilience on serverside,  let architecture deal with it - EDA - show fav pattern here
Client validation as well as server-side validation.

You'll think you're doing a good job by including a resilience framework to `exponentially backoff` or `circuit break` downstream dependencies.  Unless this is done properly and is known by all dependents, you've creating a huge problem for others!  This is one of the reasons I hate HTTP chaining.  You are inadvertantly create something that may cause of DDoS on your own infrastructure...event with a jitter!

**TODO: More content required**

## Offload cross-cutting concerns 

This is in keeping with the DRY principle.  Heres a list of typical cross-cutting concerns you can achieve through the use of an API Gateway:
- authentication & authorization
- service discovery integration
- response caching
- retry policies, circuit breaker, and QoS (quality of service)
- Rate limiting and throttling
- Load balancing
- Logging, tracing, correlation
- Headers, query strings, and claim transformation
- IP whitelisting

What this gives you is a smaller attack surface areas as well as `facade`-esque capability to re-utilisize your services.  I am not saying that this, or aggregation, are the best solutions, more that they are just solutions that may work for your particular problem domain.  This by itself is NOT the entire solution but will help your architecture a better your solution.

Take authorization for instance.  Implementing this repeatedly across all your services is simply a waste of time and effort.  The best place to target this, is at the API Gateway.  Done once, which means less code, less maintenance, one place to have your X.509 certificate.  imagine what I pain it will be having to redeploy a new X.509 certificate?!  Mind you, not so much of a pain if you have your CI/CD pipelines configured!

## EDA between domains

If you've ever done DDD (domain driven development) or even attempted to group your services whtin bounded contexts, you will still have found you need data from another domain.  Think Orders and Logistics.  These typically will sit behind different domains.  There will be a time that these will need to be request information from one another.  What about reporting, especially, executive level summary reporting?  Each domain or even bounded context may/will have their own data repositories will will lead to some complicated and drawn out conversations when solutionizing!  Let's bounce back to requesting data.  Typically, you'd use a RESTful approach to this or even gRPC - both using HTTP/2.  Either way, hopefully you will have configured internal/private HTTP APIs, as need to make this public even if they're only required by the backend services right?  The thing about HTTP traffic is that, it offers a point of failure, notice I'm saying single point of failure....we wouldn't do anything this silly would we?!  For HTTP calls, we'd, again, typically have it wrapped up using some nice resilience framework yes?  So, if the first attempt fails, we'd try again immediately then start to expotentially back off right?  And for good measure, we'd have a jitter too yes?  Well, if you have multiple clients doing this and the is several levels deep, then you could have a real problem on your hands if you have a failure that is is nested pretty deep.  All, because a web form or similar is requiring data, causing a ripple affect across all your HTTP services!

A better way to manage this is to have one BFF, that has access to all required to services that one web form request AND does NOT require need to violate cross-boundary calls. EDA (`E`vent `D`riven `D`evelopment) offers a solution to this.

What the illustration below articulates is a way to have immediate across to data that is mutated in other domains, negating the need to having to request it each and everytime a web request is made:

![](../img/2020-05-18-14-40-59.png)

The data repositories above, as used by the consumers, are all denormalized, configured specifically for reads instead of writes.

## Only take the microservices route if this addresses the issue

... you're trying to solve!

For everything we do, we need to justify it to someone above, whether they be senior management, stakeholders or even the board.  If we don't, it simiply won't get done. There's always some other priority that is more important right?!  So, the best way to justify a piece of work, albeit dealing with Tech Debt, is you have it to back it by meaningful metrics.  You can't, and you shouldn't, deem something worth doing because of your gut...if we build it, they will come kind of thing which is a no-no.

This will help enormously by forcing you to identify the ACTUAL problem you're trying to solve.  Anyone who's trying to move away from Microservices will atest to the fact that they are not always the way to go.  Even if there's a lot of evidence present to suggest this.  Monolithics, I've been reading a lot about this reversal recently, in one form or another (single process, modular and distributed) might give you, dependant on your scenario, equal or even more that one the complexities and maintenance overhead that Microservices brings to the game!  But, different horses for courses.  Just don't always be swayed by the new shiny.  Serverless obviously is the exception to the rule ;O).

All I'm going to say in the section is, always address the ACTUAL problem, and then let the best solution lead the technology choice, not the other way around.

## Cross-boundary requests metrics

OMG, how easy is this??!!  Next!

There's no excuse for not knowing how perfomant your solution is, and where fault responsibilities are.

If you are calling across bounded context, notice I didn't say domains here, or when calling out to external services, you MUST capture latencies and errors rates.  I'm not focusing here on the other 2 pillars of observabililty (tracing & logging), although, all 3 pillars are super important.

If you want a positive `Customer experience`, you must be the first to know about an failure.  Alerting will help here.  Alerting will tell you when a latency is creeping up or worse, when errors have started to happen.  Either way, you need a mechanism to give you the maximum about of time to address an issue if it arises.  Most monitoring solutions come with alerts that allow you to notify people/teams.  There are even feature that automatically assign the investigation to teams or specific indivuals.  Slightly off topic here, but only just, error management is hugely important to your organisation.  If used correctly, it can give you instant information, including all logging up to the actual error, even cross boundary in the error happened down stream in another internal service.  It pays for itself and takes the guessing out of most of the debugging effort.  This used to capture releases if powerful and again can help you pinpoint rapidly reasons for issues.

## Document as you go, must not be an afterthought

As we rapidly move from one task to the next, our memory of a prior piece of work will deminish.  Along with this will be the bigger picture.  I find documenting as you go is super useful.  Any not jsut for you, for your organisation.  I dreed to think how much time per year is spent on hunting down domain knowledge and reading through line at line of code.  Not to mention, inadvertantly implementing something that does adhere to the big picture simply because you were not aware.

What helps for me is, capturing thoughts and possble solutions.  This leads to better articulation with your colleagues.  It also helps explaain Tech Debt.  How often have you had a "Why on earth did they do this?!" moment?  One of my sayings is always respect the legacy as, more oftern than that, it was the right thing to do at the time...you weren't there mannnnn!  Everyine can be be scinic after the fact!  So, to stop you coming off as an idiot with your colleagues, document you though process, final solution, your rationale and any Tech Debt (see section above).

## No initial design will ever play out 

I've been doing this for years and never has anything ever played out exactly as first envisaged.  If anyone tells you different then they have never developed or architected anything, ever, EVER!  It's that simple.  This leads on nicely with the next section...

## It's 100% ok to change your mind

It is 100% ok to change your mind.  If you're working for an organisation which makes this awkward or embarrasing then leave.  This does not include banter.  Banter is good.  We must do our utmost to make the environment we work in, a safe one.  One where it is absolutely o'kay to make mistakes in and one where you can change your made.  These, simply put, are good engineering practices and good leadership.  Don't forget, if you're changing your mind, it is for the good of the organisation and not for shits and giggles.  If you make a mistake, and it's a whopper, then this is the fault the leadership team and not you.  Mistake don't include deliberate and melcious actions.  You do not stand by and let you colleagues make mistakes either.  Mind you, if you did, people will have already the cut of your jib.  Engineers, we're generally the good guys and girls who are morally uncorruptable and know right from wrong AND will move heaven and earth to help our colleagues.  Not sure you can say the same with other types of occupations.  Respek!

## Monorepos for shared code - can still scale by deploying to separate docker containers

I read a blog post recently where it said monorepos can't scale... "Calling Bullsh*t!"

I've recently ventured into monorepos and the services I've developed, that relay on shared code, can absolutely be scaled.  It just so happens there's no need for them too, but there is a requirement for them to be HA (highly available) which isn't the same thing but still requires at least one of an instance type to be running and to be able to recover gracefully if it does "sh*t the bed".  So for our requirement, a monorepo works just fine.

I cover this in another post I'm in the process of writing and I will include the link here when published; promise!

## Code to interfaces for HTTP API calls

One pattern that was shown to me not so long ago involved using a proxy as the gatekeeper to a HTTP API service.  This client can be either server-side or front-end facing.  The responsibility of this pattern is to guard against any breaking changing introduced unwittingly.

So, why is this important?  Imagine you have an HTTP API.  It has several controllers (endpoints), that are called from other HTTP APIs, services or the browser.  Unless, your documentation is 100% up-to-date and you and, more importantly, your colleagues are ruthless in the pursuit of removing unaccessible code, you will end up with code that just isn't used but will inevitably need to be maintained.  Also, how often have you experienced your code, in production, just stop working? Only to find out that an endpoint signature has changed?!!!! WFT!!

This is where this pattern comes into its own.  By shimming a service client in between the service and the client, you mitigate against any breaking changes.

![](../img/2020-05-18-16-33-34.png)

## Keep code simple, doesn't need to be clever.

OMG! How much time is wasted reading OR debugging complex code?!  Especially asynchronous code (thinking of .NET Core here).  God forbid your attention being distracted for a nano-second and having to start over again!  I am not saying there must not be any complex code, ever.  Some software requires complex code but surely not a web application right?

## Controllers (thin MVC) should be thin/light - biz logic in models

I've seen my fair share of controllers that contains line after line after line of business logic.  Business logic has no place in a controller. 

Another, slightest of going off topic here, is UX principles.  I've always advocated that, if a client (think brower) moves away from page having issues a request but yet in receipt of that request's response, a pre-nav call should issue a cancel.  You can do this easily with ASP.NET Core.  This Cancellation token can be passed onto down and intervine in that executing request.  It's good practice but not often seen.

Antoher advocated UX principle is to storie all form values in the browser's local storage to safe guard against catastropic failure, which may stop the user from submitting their input to the host or service endpoint. Client side code can then remind the user of uncommit changes at a later date.

## Open Source - the not so "promised land"

`Message driven development` - 12 factors. Limit where you include as you may have to replace or refactor out

## Splitting data out across microservice

What is meant to be kept together, should be kept together

## References

- [direct client to microservices Vs API Gateway pattern](https://docs.microsoft.com/en-us/dotnet/architecture/microservices/architect-microservice-container-applications/direct-client-to-microservice-communication-versus-the-api-gateway-pattern)
- [gateway offloading](https://docs.microsoft.com/en-gb/azure/architecture/patterns/gateway-offloading)