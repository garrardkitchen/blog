---
title: ".NET Stack, Heap and Boxing"
date: 2022-02-04T08:38:16Z
draft: false
tags: csharp, .net, stack, heap, allocations, gc, boxing
---

This week I have been investigating how to reduce memory allocation in a few HTTP APIs.  I won't go into any explicit work related examples here but I will touch on facets relating to this effort.  

Let's start off by looking at `Reference Types` and `Value types` and how they get allocated into the heap,.  I will also touch on concepts such as boxing and GC pressure.

Let me start off with some facts:

- A `Reference types` always get allocated on the heap
- A `Value types` _mostly_ get put on the stack, but _do_ get placed on the heap sometimes.
- If `Value type` is hoisted (top level class field) on a class, this gets allocated on the heap along with it's parent.
- If `Value type` is boxed, this too gets allocated on the heap

Yikes, confusing yes?

## Examples

I'm going to break off in to some examples next to help explain _why_ and _when_ `Value Types` get allocated.

**Example 1: A Class Field**

In this class, we can see that _attempts is a Value Type:

```csharp
public class NewOrder
{
    private int _attempts = 0;
    
    public void PlaceOrder() {}
}
```

Due to it being a class field, it is allocated on heap with it's parent `NewOrder`.  This is seen here:

![](../img/2022-02-04-09-08-06.png)

The same will happen if you hoist a Struct to the root of the class.  Take this code for instance, the Item struct is placed on the heap along with it's parent (see screengrab below):

```c#
public class Order
{
    private Item _item = new Item();
    
    public Order() {}
}

public struct Item
{
    public int Id { get; set; }
    public string Sku { get; set; }
}
```

![](../img/2022-02-04-13-15-28.png)

**Example 2: Boxing**

Class Properties `Value Types` don't get placed on the heap.  However, if they get `boxed` (eg via string interpolation, method group, lamba expression), then they do.

This example shows the result on the ItemCount property after it gets boxed via string interpolation:

![](../img/2022-02-04-09-39-50.png)

## Concepts

_Boxing and unboxing_

**Boxing** is process of converting a `Value Type` to the type `object` (aka implicit conversion). It creates a new allocation in the Heap, copies in the `Values type` value and returns a pointer. 

See the last int32 instance in this screengrab:

![](../img/2022-02-04-10-01-21.png)

**Unboxing**, on the hand, is the reverse and is the process of converting a type `object` to a `Value Type` (aka explicit conversion).

In this example, you see that the unboxing doesn't get allocated onto the Heap:

![](../img/2022-02-04-10-05-14.png)

## GC Pressure

You my be asking the question, "why is any of this important?"

One reason it will become relevant is if you are observing GC pressure.

GC pressure means that the GC is feeling the strain and increasingly becoming overwhelmed deallocating memory.  This _could_ be the result of an incorrect GC configuration.  If you're not careful, your production docker container workload(s) may not have adeqaute available private memory.  If this is the case then GC will be working twice as hard to avoid an OOM exception.  Lack of memory will kill your app.  Lack of CPU however will simply throttle your app and will not result in your app being kill.  There is one example when this isn't exactly true.  Let's say you have a pod running in your kubernetes cluster, and your configuration includes both Liveness and a Readiness probes.  If your CPU is maxed out and your HTTP Listener can't receive and respond to HTTP requests during the time the probes parameters allow (combination of periodSeconds, failureThreshold and timeoutSeconds), then neither probe will have the availability to inform AKS that it's still alive but just busy so don't shut me down.  So, the inevitable will happen. Yes, AKS will kill your pod and restart another; providing you've a ReplicaSet configured.


Reviewing your code and identifying changes that can reduce allocation, will help.  It's not the only approach.  More often than not though, especially with a focus on using less space (Big O Notation), will result in larger method frames, plus verbose code, plus determinate collection sizes, etc... .

Also worth noting here too is that whenever GC executes, your running application will stop and will resume once GC completes.

To reiterate an earlier point, there are many approaches to avoiding GC pressure.  These are well documented and are easily found on the internet.  I've included several below for completeness:

- Avoid memory leaks (big topic!)
- Use  (when appropriate) in place of a Class
- Usng a StringBuilder correctly
- Avoid finalizers
- Setting the initial seize of a _dynamic_ collection
- ArrayPool for short lived arrays (large)

## References

- [Boxing/unboxing](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/types/boxing-and-unboxing)