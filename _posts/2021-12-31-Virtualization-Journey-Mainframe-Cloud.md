---
layout: post
title:  "Journey of Virtualization from Mainframe to Cloud"
author: ming
categories: [ Mainframe, Cloud, virtualization, container]
tags: featured
image: assets/images/2021/Virtualization/Journey of Computing Virtualization.svg
published: true
---

If I ask you a layman question:
>What is the difference between a computer server and PC?

A quick but distinctive answer I can give is, a server is able to handle multiple different workload for multiple users simultaneously. In pro IT words, server can virtualize its underlying hardware resources to serve different environments, systems or even multiple tenant of customers.  

![]({{ site.baseurl }}/assets/images/2021/Virtualization/Journey of Computing Virtualization.svg)

Cloud computing has been the great adoption of massive virtualization where customers don’t need to purchase their own physical computer hardware or even software. But long before cloud was introduced in 2006 and widely used commercial virtualization hypervisor like VMware was founded in 1998, computing platform virtualization had evolved from mainframe OS since its 1st generation of OS/360 on S/360. In a nutshell, virtualization has gone through below stages: multiple addresses in one OS -> virtual instance (LPAR) -> clustering (parallel sysplex) -> cloud -> container (docker, K8S)

![]({{ site.baseurl }}/assets/images/2021/Virtualization/VM to K8S.svg)

The mainframe in the 1960s is pretty much similar to desktop PC we use today, a monitor as terminal display, keyboard and printer as input and output, and data is stored on storage system and processed by CPU. The operating system allows user to perform multiple tasks at the same time like writing this blog and surfing internet using different applications concurrently, in mainframe, such application runs in its own virtual storage called address space (System tasks also called STC, batch as JOB). ie one DB2 or CICS instance runs in independent address space with isolated real and virtual memory allocated. The early version of mainframe OS - Multiple Virtual Storage serves as the Unix Kernal to manage the data access and control memory swapping.

![]({{ site.baseurl }}/assets/images/2021/Virtualization/MF addr.svg)
