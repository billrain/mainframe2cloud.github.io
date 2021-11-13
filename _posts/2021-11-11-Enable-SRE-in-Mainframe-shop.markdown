---
layout: post
title:  "Enable SRE in a Mainframe shop"
author: ming
categories: [ Mainframe, SRE]
tags: featured
image: assets/images/2021/SRE/MF2C_SRE_Value_Stream.png
published: false
---
# Backgroud
Many Fortune 500 companies have been running Mainframe (IBM System z) for decades and established comprehensive infrastructure and operation model. With emgering cloud and open source technologies, IT operation has been shifting from tranditaionl 'system administration' to Site Reliability Engineering, should mainframe shops embrace the trend and transform their engineering and ops model to SRE?

# What is Site Reliability Engineering?
SRE was brought up by Google back in 2003 which was even earilier than inception of DevOPS.

>Google: SRE is what happens when a software engineer is tasked with what used to be called operations
IBM: SRE leverages operations data and software engineering to automate IT operations tasks, and to accelerate software delivery while minimizing IT risk
Apple: SREs at Apple own the full infrastructure stack; from device driver performance debugging to content delivery network traffic management — our responsibilities are both broad and deep
Microsoft: Site Reliability Engineering is an engineering discipline devoted to helping an organization sustainably achieve the appropriate level of reliability in their systems, services, and products

Different big tech shops have different definition about SRE but from IT infra, service and operation point of view, the main idea is cohesive to "Operate applications in production “mission-critical systems” and do whatever is necessary to keep the site up and running".

# Why there is a need to shift?
IT operations teams have deployed monitoring tools for decades to track the performance of infrastructure, networks and applications that support business processes. As the IT landscape evolves, monitoring tools have shown limitations in their ability to adapt to the volatility of these architectures. Static dashboards with human-generated thresholds do not scale to these modern environments and are inflexible in assisting the resolution of unforeseen events. Using these tools, the business is unable to determine the state of its applications with a high degree of certainty and understand how their services impact business key performance indicators (KPIs) and customers’ digital experience. To deliver the digital experience necessary to remain competitive, enterprises, and not just their SREs, must go beyond infrastructure and make their digital business observable.

## Value Stream

## SRE vs Tradtional I&O

### Observability
“Observability is the characteristic of software and systems that allows them to be “seen” and allows questions about their behavior to be answered.”  -- Gartner
Monitoring, as commonly implemented, relies on building dashboards and alerting to escalate known problem scenarios when they occur. 
Observability enables quick interrogation of a digital service to identify the underlying cause of a performance degradation, even when it has never occurred before.
