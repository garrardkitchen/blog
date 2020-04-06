---
title: "Kubernetes on Windows"
date: 2020-04-05T17:31:26+01:00
draft: false
featured: true
tags: [blogging, kubernetes, setup, windows, containers, pods, services, azure, aws, gcp]
---

This post is a reminder of what needs to be installed in order for a `pod` created from a local image that is to be served up via `kubernetes cluster`, all from your local development environment.

### What is Kubernetes and why is it so important?

"Kubernetes is a portable, extensible, open-source platform for managing containerized workloads and services, that facilitates both declarative configuration and automation. It has a large, rapidly growing ecosystem. Kubernetes services, support, and tools are widely available."

### So why is Kubernetes important?

"`Containers` are a good way to bundle and run your applications. In a production environment, you need to manage the containers that run the applications and ensure that there is no downtime. For example, if a container goes down, another container needs to start. Wouldn’t it be easier if this behavior was handled by a system?

That’s how `Kubernetes` comes to the rescue! Kubernetes provides you with a framework to run distributed systems resiliently. It takes care of scaling and failover for your application, provides deployment patterns, and more. For example, Kubernetes can easily manage a canary deployment for your system."

`Kubernetes` is the community's (has a much larger community than that of `Swarm's` community) choice of `container` `orchestrators`.

### Some important notices  

{{< note title="Permissions">}}
To install <b>kubectl</b> and <b>minikube</b> you must start Powershell with Administrator permissions
{{</ note >}}

{{< note title="Shell" warning="true">}}
These settings will only viable for the current shell, if you need to run another shell, ensure the <b>minikube docker-env</b> commands in the <b>Steps to take to configure your environment</b> are also executed in the new shell
{{</ note >}}

### How do I install `kubectl` (and what the heck is it)?

[**kubectl**](https://github.com/kubernetes/kubectl) is a CLI (command line interface) tool for controlling Kubernetes clusters.  You can use this tool to deploy applications, inspect and manage cluster resources and view logs.

To ease the installation process, use [chocolatey](https://chocolatey.org/packages) to install [kubernetes-cli](https://github.com/kubernetes/kubectl), run:

```ps
PS C:\> choco install kubernetes-cli
```

### How do I install minikube (and what the heck is it)?

`Minikube` implements a local Kubernetes cluster and is deemed the best tool for local Kubernetes application development.

To ease the installation process, use [chocolatey](https://chocolatey.org/packages) to install [**minikube**](https://github.com/kubernetes/minikube), run:

```ps
PS C:\> choco install minikube
```

Before you start, you must ensure that you have a virtualisation platform available.  Windows 10 comes with a virtualisation feature called `hyper-v`. You need to ensure it is running first.  To do this, run:

```ps
PS C:\> Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```

```ps
PS C:\> minikube start --driver hyperv
* minikube v1.9.1 on Microsoft Windows 10 Enterprise 10.0.18363 Build 18363
* Using the hyperv driver based on user configuration
* Downloading VM boot image ...
    > minikube-v1.9.0.iso.sha256: 65 B / 65 B [--------------] 100.00% ? p/s 0s
    > minikube-v1.9.0.iso: 174.93 MiB / 174.93 MiB [ 100.00% 1.03 MiB p/s 2m51s
* Starting control plane node m01 in cluster minikube
* Creating hyperv VM (CPUs=2, Memory=6000MB, Disk=20000MB) ...
* Preparing Kubernetes v1.18.0 on Docker 19.03.8 ...
* Enabling addons: default-storageclass, storage-provisioner
* Done! kubectl is now configured to use "minikube"
```

### Steps to take to configure your environment

To set up your minikube environment, run:
```ps
PS C:\> minikube docker-env
$Env:DOCKER_TLS_VERIFY = "1"
$Env:DOCKER_HOST = "tcp://192.168.75.126:2376"
$Env:DOCKER_CERT_PATH = "C:\Users\garrard.kitchen\.minikube\certs"
$Env:MINIKUBE_ACTIVE_DOCKERD = "minikube"
# To point your shell to minikube's docker-daemon, run:
# & minikube -p minikube docker-env | Invoke-Expression
```

To point your shell to minikube's docker-daemon, run: 
```ps
PS C:\> minikube docker-env | Invoke-Expression
```

To get access to minikube's dashboard, run: 

```ps
PS C:\> minikube.exe dashboard
* Verifying dashboard health ...
* Launching proxy ...
* Verifying proxy health ...
* Opening http://127.0.0.1:54553/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/ in your default browser...
```

Here's some sample node.js code. It starts a server on port 8080:

```js
var http = require('http');

var handleRequest = function (request, response) {
    console.log('Received request for URL: ' + request.url);
    response.writeHead(200);
    response.end('Hello World!');
};
console.log("started")
var www = http.createServer(handleRequest);
www.listen(8080);
```

Here's a `Dockerfile` for the above `nodejs` server. Please observe that it exposes port 8080. This ensures that network TCP traffic can be received by the container via port 8080.

```Dockerfile
FROM node:6.14.2
EXPOSE 8080
COPY server.js .
CMD [ "node", "server.js" ]
```

To build a image of the above `Dockerfile`, run:
```ps
PS C:\> docker build -t hello-world:1 .
```

To run this image as a pod, run:
```ps
PS C:\> kubectl run hello-world --image=hello-world:1 --port=8080 --image-pull-policy=never
pod/hello-world created
```

The `--image-pull-policy=never` is telling Kubectl to use the local image and not one from a container registry ([Docker](https://hub.docker.com/), [ACR](https://azure.microsoft.com/en-us/services/container-registry/), [ECR](https://aws.amazon.com/ecr/), [GCP](https://cloud.google.com/container-registry)) 

To expose this port for external access (from browser) from outside of the cluster, run:
```ps
PS C:\> kubectl expose pod hello-world --type=LoadBalancer
service "hello-world" exposed
```

To confirm your `service` is running and to get the `port number` of this `exposed service`, run:
```ps
PS C:\> kubectl get services
NAME          TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
hello-world   LoadBalancer   10.111.126.10   <pending>     8080:31589/TCP   45h
kubernetes    ClusterIP      10.96.0.1       <none>        443/TCP          2d16h
```

{{< note >}}
You will see the <b>&ltpending&gt</b> state of your LoadBalancer if you do not have not a Load Balancer integrated with your cluster.  For your local development environment, it is nothing to worry about. 
{{</ note >}}

You will see that the **hello-world** service is accessible via port **8080**. However, we still don't know behind what IPv4 address, this services is available.  To get the IPv4 address of your cluster, you type:

```ps
PS C:\> minikube ip
192.168.75.126
```

Finally, to access your service, run the cURL command, using the `minikube ip` address and the TCP port as listed in the `kubectl get services` output:
```ps
PS C:\> curl "http://192.168.75.126:31589" -UseBasicParsing
StatusCode        : 200
StatusDescription : OK
Content           : {72, 101, 108, 108...}
RawContent        : HTTP/1.1 200 OK
                    Connection: keep-alive
                    Transfer-Encoding: chunked
                    Date: Mon, 06 Apr 2020 13:05:42 GMT

                    Hello World!
Headers           : {[Connection, keep-alive], [Transfer-Encoding, chunked], [Date, Mon, 06 Apr 2020 13:05:42 GMT]}
RawContentLength  : 12
```

### Useful `kubectl` commands

This first command is important. Some background first...a `context` is a group of access parameters. Each `context` contains a `Kubernetes cluster`, a `user`, and a `namespace`.  When you are working with multiple contexts off of your development machine, you may run into compatibility issues due to your client version not being compatible with the server API version. All `kubectl` commands will run against the `current context`. To check your client version, run:

```ps
PS C:\> kubectl version --client
Client Version: version.Info{Major:"1", Minor:"18", GitVersion:"v1.18.0", GitCommit:"9e991415386e4cf155a24b1da15becaa390438d8", GitTreeState:"clean", BuildDate:"2020-03-25T14:58:59Z", GoVersion:"go1.13.8", Compiler:"gc", Platform:"windows/amd64"}
```

To ascertain your current context, run:
```ps
PS C:\> kubectl config current-context
minikube
```

To list all of your configured contexts, run:
```ps
PS C:\> kubectl config get-contexts
CURRENT   NAME                 CLUSTER          AUTHINFO         NAMESPACE
          docker-desktop       docker-desktop   docker-desktop
          docker-for-desktop   docker-desktop   docker-desktop
*         minikube             minikube         minikube
```
The * next to **minikube** indicates that **minikube** is your current context.

If you are configured to access cluster(s) hosted by a cloud provider such as `Azure`, then this context will also be listed here.



To use a specific context, run:

```ps
PS C:\> kubectl config use-context docker-for-desktop
```



## References

- [Install Kubernetes](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
- [Install Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/)
- [Kubectl Cheatsheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)