---
title: "Github Actions Workflows Syntax Windows and Linux"
date: 2022-01-08T11:09:12Z
tags: github actions, linux, windows, syntax, self-hosted runner, environment variables, workflow, cicd
---

# Runtimes

In my current role as Head of Cloud Platform, I am leading the technical effort of migrating our entire on-premise real-estate to Azure.  Part of this mission, is to upgrade the runtimes of our applications, regardless of their current placement; IIS Web apps, Windows Services and swarm docker containers.  I say "part of this mission" as another aspect of this migration is to create a new foundation for our platform - AKS.  I hope to cover more on this in later posts.

With being deliberately ambiguous on actual numbers here, we have a _fair_ amount of workloads that are running on runtimes that have surpassed their end of life support status 😱.  I am sure we're not the only organization that finds itself in this predicament.  One of the first things our CTO wanted when he came onboard was for us to sort out our runtimes.

What this means is that we have .NET Framework and .NET Core workloads that need to be migrated to a runtime that is not out of support (now or anytime soon).  At the same time, the target runtime has to have hit the mature (at least on it's 1st minor release) status.  .NET 6.0 is now GA but it's paint is still a little wet.  Understandidly there is some concern, borderline trepidation towards targeting this new version.  There are impactful benefits (performance, stability, improved GC, cost benefits, etc...) and our current runtime target for our .NET Core workloads is 3.1.  However, end of life support for this particular version is the end of this year - [03.Dec.2022](https://docs.microsoft.com/en-us/lifecycle/products/microsoft-net-and-net-core).  So, at somepoint, this upgrade needs to happen.  With planning and priorisation, this can happen when appropriate.

One strategy of deliverying confidence with a runtime is to update a few apps, consecutively; providing you've availability to accommodate such an approach.  Timeboxed: (1) A simple (CRUD-esque HTTP API that sits on top of a Db) workload then (2) one that involves an `data-in-motion` aspect (message queue, consumer paradigm, non-http api).  

Another approach to reducing concern, mitigating risk, etc... is to meet with the SME (Subject matter expert) - a Microsoft representative - and listen to what they have to say.  I have orchestrated such a meeting - we are being helped/guided by Microsoft FastTrak.  Advice received was to upgrade.  We were also reminded of the migration paths to take when moving to a target version.  Here are some demonstrative links that will help you through this process:

- Migration guidance found here ➡ [migrate from asp.net core 3.1 to 6.0](https://docs.microsoft.com/en-us/aspnet/core/migration/31-to-60?view=aspnetcore-6.0&tabs=visual-studio).  

- An exampe of breaking changes between version can be found here ➡ [breaking changes in .NET Core 3.1](https://docs.microsoft.com/en-us/dotnet/core/compatibility/3.1)

Most of our workloads are simple HTTP API CRUDs that sit aloft databases.  In short we're not doing anything too complicated and so this makes most of our pain points the result of external NuGet package dependencies.

Anyways, back to the topic of this post!

# Github workflows

We're targeting 2 guest operating systems with our containerisation orchestration solution - AKS.  These are linux (.NET Core workloads) and Windows (.NET Framework workloads).  We are having to upgrade our .NET Framework runtimes to 4.8 as this is the minimum requirement to running containers in Kubernetes in Azure.

There are subtle GitHub Actions Workload expression differences when working with Powershell and bash.  I'll drill into these subtlies below.

Here is a snippet from our `deploy` GHA workflow.  We use a workflow_dispatch to deploy either feature branches or our main branch.  Feature branches are deployed to our Development Cluster and non-feature branches to our Production Cluster.

```yml
- name: SETUP MAIN BRANCH
  if: ${{ github.ref == 'refs/heads/main' || github.ref == 'refs/heads/master' }}
  run: |    
    echo "ENV=prod" >> $GITHUB_ENV        
    echo "TAG=1.0.${{github.run_number}}" >> $GITHUB_ENV       
    echo "PWD=$(pwd)" >> $GITHUB_ENV        

- name: SETUP FEATURE BRANCH
  if: ${{ github.ref != 'refs/heads/main' && github.ref != 'refs/heads/master' }}
  run: |
    echo "ENV=dev" >> $GITHUB_ENV        
    echo "TAG=${{ github.ref_name }}" >> $GITHUB_ENV
    echo "PWD=$(pwd)" >> $GITHUB_ENV        

```

In the example above, we generating env vars that are used later in this workflow.  The above is running on one of our linux self-hosted runners so using bash script.

This above example will not update env vars when run on a windows self-hosted runner (powershell).

The equivalent when targeing windows is:

```yml
- name: SETUP MAIN BRANCH
  if: ${{ github.ref == 'refs/heads/main' || github.ref == 'refs/heads/master' }}
  run: |        
    echo "ENV=prod" >> $env:GITHUB_ENV        
    echo "TAG=1.0.${{github.run_number}}" >> $env:GITHUB_ENV       
    echo "PWD=$(Get-Location)" >> $env:GITHUB_ENV        

- name: SETUP FEATURE BRANCH
  if: ${{ github.ref != 'refs/heads/main' && github.ref != 'refs/heads/master' }}
  run: |
    echo "ENV=dev" >> $env:GITHUB_ENV        
    echo "TAG=${{ github.ref_name }}" >> $env:GITHUB_ENV
    echo "PWD=$(Get-Location)" >> $env:GITHUB_ENV        
```

Notationally, the only difference here is `>> $GITHUB_ENV` and `>> $env:GITHUB_ENV`.  Powershell requires the presuffix of `env:` (as in `Get-ChildItem env:`).  The consumer syntax of this env var is the same - `${{ env.TAG }}` so it's only the publishing of this env var that needs to change between shells.

Here's an example of where this env var is being consumed:

```yml
- name: BUILD AND PUSH
  run: |
    try {
        cd ${{ env.ROOT_DIR }}   
        docker build -t ${{ env.ACR_NAME }}/${{ env.APP_DOCKERIMAGE }}:${{ env.TAG }} -f ${{ env.ROOT_DIR }}/${{ env.APP_DOCKERFILE }} .
        docker push ${{ env.ACR_NAME }}/${{ env.APP_DOCKERIMAGE }}:${{ env.TAG }}
    } catch {
        Write-Output "Could not push to ACR, error occured with docker build"
        Exit 1
    }        
    
```

# CICD

{{< hint info >}}

We are using the approach of regenerating feature images and deploy from same workflow instead of deploying to our Development cluster triggering from a merge to a development branch due to the fact a dedicated development branch might get out of sync with main easily if your branching strategy predicates promoting to production via a merge to main from a dedicated development branch.  We often have multiple developers concurrently working on the same repo so this in itself presents inherit complexity so arriving at a CICD pipelines wasn't clear cut as teams have subtle nuances around how to build & deploy features/fixes.

{{< /hint >}}

