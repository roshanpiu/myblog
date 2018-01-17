---
title: Introduction to Docker
date: 2018-01-17 10:36:07
tags:
---

## How appication deployment used to work in the past

In order to fully understand the importance of docker containers. First we need to take a brief look at the IT history.
back in the day we used to build servers and applications in a one to one ratio (server (1) : application (1)).
for every application we needed to deploy we needed to buy and setup a dedicated server for it.
if we had 10 applications we needed 10 physical servers. that means for every application we need to deploy
we had to buy a server install an OS, setup storage, cooling and networking. every application needed a ton of infrastructure
work. this makes the process of deploying applications very tedious and very time consuming.
most of the time we only use the servers we built at a tiny fraction of their true capability sometimes the resource usage of the server is
is under 10% as you can understand this is a massive waste of resources.

![capture 1](/content/images/introduction-to-docker-images/Capture1.PNG)
## Virtualization

so clearly there was a problem and it needed to addressed. A solution was invented an it was virtualization.
we take a the physical machine and we carve it up to bunch of virtual machines.  Every virtual machine looked feels works like a physical machine. This is done by using a hypervisor layer.
This enabled us to run multiple applications in a single physical machine. With out a dought this was a much better model and enabled us to utilize resources of our severs
Better and reduce waste. Even though this approach was a massive improvement from the old model this approach was far from perfect.

![capture 2](/content/images/introduction-to-docker-images/Capture2.PNG)

## Virtual machine pitfalls

Let’s look at why the VM model was far from perfect.
Out of everything there is the application is the most important piece. the OS should only exists to facilitate the application. In vm model every application needs a full blown operating system. So what we have really done in the vm model Is trim off the excess of the physical layer. Even though we could run ten applications in one physical machine we needed 10 different operating systems to do that. Every OS consumes so much resources and some operating systems needs individual licensing.  OS adds no business value and each OS instance was a massive waste of resource.
It should be all about the application. All app needs to run is a secure isolated environment with minimal OS services and some service control. While the VM model satisfies these requirements the waste of resources and the cost is too much to overlook. As you can see the VM model is far from ideal so we needed to look for a better approach.

## Containers

The solution for the bloated VM model was containers.
Containers are a application runtime environment. The most important difference of containers from VMs are containers are way more light weight that VMs. Each container consumes way less CPU, RAM and Disk space than a VM. But still containers provide a secure runtime environment for applications to run.
So how is a container is so much lighter than a VM?. In VM model we needed to install a full blown OS inside every VM. Each OS inside the VM consumes so much of CPU, RAM and Disk space.
On the other hand containers does not require a full blown OS for each container. It can share the OS kernel on the physical machine.

![capture 3](/content/images/introduction-to-docker-images/Capture3.PNG)

## Why Containers?

So you might think why do we need containers? Why can’t we deploy multiple applications on a single operating system? The answer is the operating systems such as linux and windows are not so good at doing that. We don’t have a good way of isolating those applications and prevent them from working all over each other. For an example imagine there are two applications installed on the same OS both use a common shared library file but each needs a different version of that file. We don’t have a great way of doing something like having two different versions of the same file on the same OS. So this a problem that containers can address. Since containers has isolated environment of userspace each container can have different version of the same file.

## Containers under the hood

Let’s look at how containers and their isolated environment of userspace are created.
In order to create an isolated environment where an application can run in. We need to create isolated instances of things like  root file systems, process trees and networking stacks. 
This means one container can have it’s own root filesystem and one app running in a container can access and modify it’s own root file system without impacting the other applications running on other containers.

![capture 4](/content/images/introduction-to-docker-images/Capture4.PNG)

Same goes for the independent view of the process tree. Each container can have it’s own PID 0 and it’s own process hierarchy under that. This means one process inside of container will not collide with a different process in a different container

![capture 5](/content/images/introduction-to-docker-images/Capture5.PNG)

Each container gets it’s own IP address and network stack.
So how is this isolation is achieved?
This isolation is achieved via features of linux kernel called “Kernel Namespaces” , “Control Groups” and “Capabilities”.
 Kernel Namespaces allows us to partition system namespaces such and process tree, networking, filesystem and user namespaces.
 So from the view of the host OS there is only one process tree. But each partition of the process tree gets assigned to one container. since these containers are isolated the processes within the container can’t interact with processes outside the container
When we partition user namespaces we can have user accounts with root privileges inside the container but not out side of the container.
Control Groups allows us to group resources together and apply limits to them. So one container has it’s own control group. This enables us to set limits to CPU, Memory and Disk space the container can access.
capabilities gives us fine grain control over what privileges a user or a process gets. We can use capabilities to grant processes only the privileges it needs.

## Docker

Docker is a container runtime. Which implements the container technology. It uses “Kernel Namespaces” , “Control Groups” and “Capabilities” to form one product. 
Docker provides very uniform and standard runtime where applications can run in. This means developers can develop applications inside docker containers on their computers and package and deploy them to cloud platforms such as AWS and Azure which has a docker runtime and expect them to behave the same.

![capture 6](/content/images/introduction-to-docker-images/Capture6.PNG)

Docker is actually is more than a container runtime it is more of the platform. It has
- An Image format
- A Container runtime
- Clustering
- Service discovery
- Networking

see the extensive glossary provided by Docker (https://docs.docker.com/v1.11/engine/reference/glossary/).
Docker enables and encourages us to microservices architecture. These are types of applications designed as smaller services that operate collectively to work as one application. Each of these smaller component services runs as a single process inside of it’s own container. These components running inside it’s own container independent and can individually be updated.