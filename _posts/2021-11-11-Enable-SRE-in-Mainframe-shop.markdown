---
layout: post
title:  "Enable SRE in a Mainframe shop"
author: ming
categories: [ Mainframe, SRE]
tags: featured
image: assets/images/2021/SRE/MF2C_SRE_Value_Stream.png
published: true
---
# Backgroud
Many Fortune 500 companies have been running Mainframe (IBM System z) for decades and established comprehensive infrastructure and operation model. With emgering cloud and open source technologies, IT operation has been shifting from tranditaionl 'system administration' to Site Reliability Engineering, should mainframe shops embrace the trend and transform their engineering and ops model to SRE?

# What is Site Reliability Engineering?
SRE was brought up by Google back in 2003 which was even earilier than inception of DevOPS.

>Google: SRE is what happens when a software engineer is tasked with what used to be called operations

>IBM: SRE leverages operations data and software engineering to automate IT operations tasks, and to accelerate software delivery while minimizing IT risk

>Apple: SREs at Apple own the full infrastructure stack; from device driver performance debugging to content delivery network traffic management — our responsibilities are both broad and deep

>Microsoft: Site Reliability Engineering is an engineering discipline devoted to helping an organization sustainably achieve the appropriate level of reliability in their systems, services, and products

Different big tech shops have different definition about SRE but from IT infra, service and operation point of view, the main idea is cohesive to:
>Operate applications in production “mission-critical systems” and do whatever is necessary to keep the site up and running

# Why there is a need to shift?
IT operations teams have deployed monitoring tools for decades to track the performance of infrastructure, networks and applications that support business processes. As the IT landscape evolves, monitoring tools have shown limitations in their ability to adapt to the volatility of these architectures. Static dashboards with human-generated thresholds do not scale to these modern environments and are inflexible in assisting the resolution of unforeseen events. Using these tools, the business is unable to determine the state of its applications with a high degree of certainty and understand how their services impact business key performance indicators (KPIs) and customers’ digital experience. To deliver the digital experience necessary to remain competitive, enterprises, and not just their SREs, must go beyond infrastructure and make their digital business observable.

Many seasoned Mainframe System Programmers(SP) may think the problem statement applies to distributed environments only and doubt the necessity for a 'centralied computing platform' like System z requires more monitoring, because being one of the most long lasting centralied computing platform, conventioal Mainframe shops have had much better observability of service health even certain level of log aggregation than contemporaneous on-prem distributed clusters, with modern Mainframe monitoring solutions, SP can utilise GUI to measure performance and health of system and services pretty much alike cloud native monitoring. Adopting SRE can maximize value of exsiting asset and improve service quaility to a higher level by practising Continuous Improvement.

![Google SRE Book](https://lh3.googleusercontent.com/3gX2qgys2I-9HnEIvXUA10ed3AILvg5MclnKWBquEkJKP3g5_kD6WR7Ptwp3TwAGla1DuSmHv64MdTtACNLlArFVq7BwbTrTVhigsA=s900)
Source: Google Service Reliability Hierarchy

## Value Stream
![]({{ site.baseurl }}/assets/images/2021/SRE/MF2C_SRE_Value_Stream.png)


## SRE vs Traditional I&O
SRE is not a disruptive innovation, many concepts or advocates of SRE oriented from best practices of ITSM that passed on by the predecessors. The tooling for different platform may vary but it also aims to operate the production runtime securely with high reliability.

<style type="text/css">
.tg  {border-collapse:collapse;border-color:#aabcfe;border-spacing:0;}
.tg td{background-color:#e8edff;border-color:#aabcfe;border-style:solid;border-width:1px;color:#669;
  font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg th{background-color:#b9c9fe;border-color:#aabcfe;border-style:solid;border-width:1px;color:#039;
  font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg .tg-0lax{text-align:left;vertical-align:top}
</style>
<table class="tg">
<thead>
  <tr>
    <th class="tg-0lax">I&amp;O</th>
    <th class="tg-0lax">Traditional</th>
    <th class="tg-0lax">SRE</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-0lax">Security by Design</td>
    <td class="tg-0lax">People, Machine, Network</td>
    <td class="tg-0lax">Same</td>
  </tr>
  <tr>
    <td class="tg-0lax">Operation Engineering</td>
    <td class="tg-0lax">Compute/Storage/Network</td>
    <td class="tg-0lax">Same</td>
  </tr>
  <tr>
    <td class="tg-0lax">System Monitoring</td>
    <td class="tg-0lax">SLA, SLO</td>
    <td class="tg-0lax">SLO, SLI, Error budget</td>
  </tr>
  <tr>
    <td class="tg-0lax">Automation</td>
    <td class="tg-0lax"></td>
    <td class="tg-0lax">Reduce Toil </td>
  </tr>
  <tr>
    <td class="tg-0lax">System Arch</td>
    <td class="tg-0lax">Availability, Durability, Scalability</td>
    <td class="tg-0lax">Same</td>
  </tr>
  <tr>
    <td class="tg-0lax">Troubleshooting</td>
    <td class="tg-0lax">Log&amp;Ops data driven</td>
    <td class="tg-0lax">AI&amp;ML driven</td>
  </tr>
  <tr>
    <td class="tg-0lax">Change Management</td>
    <td class="tg-0lax">SDLC</td>
    <td class="tg-0lax">CI/CD</td>
  </tr>
  <tr>
    <td class="tg-0lax">Culture </td>
    <td class="tg-0lax">Ownership </td>
    <td class="tg-0lax">Blameless postmortem</td>
  </tr>
  <tr>
    <td class="tg-0lax">May-day</td>
    <td class="tg-0lax">Depends on BAU workload</td>
    <td class="tg-0lax">50% focus on improvement</td>
  </tr>
</tbody>
</table>

### Observability
>Observability is the characteristic of software and systems that allows them to be “seen” and allows questions about their behavior to be answered. -- Gartner

Monitoring, as commonly implemented, relies on building dashboards and alerting to escalate known problem scenarios when they occur. Whereas observability enables quick interrogation of a digital service to identify the underlying cause of a performance degradation, even when it has never occurred before. By extending measurement to cover more services and analysing the uncovered iceberg, I&O can provide quicker RCA and more insight to our customers, therefore engineers will have better chance to produce more impactful engineering work.

<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/a/ac/Iceberg.jpg/824px-Iceberg.jpg" alt="drawing" width="500"/>

In recent [Data-driven hypothesis development](https://www.thoughtworks.com/en-us/insights/articles/data-driven-hypothesis-development) published by thoughtworks, problems are classified into four categories:

![](https://www.thoughtworks.com/content/dam/thoughtworks/images/photography/inline-image/insights/articles/ar_inline_data_driven_1.png)

Mainframe has always been the system or records which produce and hold various and abundant operation data and logs, they are like fossil fuel hidden underground, not all are exploited to enable data driven development and decesion making. Building new observability capability is essential to unveil more unknowns into knowns and mitigate dynamic risks.  
