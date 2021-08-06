---
layout: post
title:  "Offload SSL/TLS to Crypto Card and Save million dollar MIPS!"
author: Ming
categories: [ CICS, Mainframe ]
tags: featured
image: https://media-exp1.licdn.com/dms/image/C5612AQEUE9gZKqScVQ/article-cover_image-shrink_720_1280/0/1613986604367?e=1633564800&v=beta&t=guDge_tmdcYWGKiVfbAJ8ueJzKJPBFTw96-5_07NEOQ
---

## Abstract
Mainframe is the backbone of commercial transactional business and million of tons of data are flying in and out between mainframe servers and their client servers. Similarly SSL/TLS is the backbone of the secure network channel for server to server communication which protects sensitive information. But encryption workload can be an overhead if your online SSL/TLS traffic is high because the encryption is done by general CP(GCP). HSM or crypto card can offload those SSL/TLS encryption work effectively from GCP, potential MIPS saving can worth million dollars in mid-long term run.

## Background
With modern IT architecture, many mainframe costumers are embracing API economy and connected to outside world via different links,  be it legacy way like simulating LU-LU connection via VTAM EE or modern ways like CICS web service or API gateway.  Mainframe applications and services are exposed to external user, partners and integration points inevitably. Secure the data transportation between service provider and consumer is critical to IT ecosystem and data governance. Data in-flight encryption or to be more specific in tech term, SSL(Secure Socket Layer) and its successor, TLS(Transport Layer Security) are the most common used and standard security technology for establishing an encrypted link between a server and a client. It is as simple as we browse any secured website using HTTPS protocol, a certificate will be presented by the website to prove it is indeed the authentic website we want to land.

Within a mainframe environment, SSL/TLS can be implemented for communications over TELNET(3270), MQ connection with distributed, or CICS web service if you are using it. And there are two kinds of SSL, we simply put them as one way SSL and two way SSL:

• One way SSL or Server Certificate Authentication(Figure 1): client validates server only, server does not verify client – required by TELNET
 (img)
• Two way SSL or Client Authentication(Figure 2): the client application verifies the identity of the server and then the server application verifies the identity of the client application. Both parties share their public certificates, and then validation is performed – most secure, required for CICS web service
(img)
When TELNET users in a mainframe environment use 3270 emulator in their own individual PC to connect to mainframe, there is higher chance they use one way SSL to authenticate server (mainframe) only, rather than using two way SSL to identify each user which will require a client SSL certificate to be installed in each user’s PC. Of course if TELNET users are accessing the host from one or several containers or virtual desktops where 3270 emulator are configured with client certificate within the container, their organization won’t need to distribute so many individual SSL certificates.

To adhere to FIPS 140-2 standard, z/OS supports Application Transparent Transport Layer Security (AT-TLS) which provides application-transparent secured connections for both client and server.

For application services like SOAP or RESTful API provided by CICS, the ‘SSL’ attribute in CICS TCPIPSERVICE resource controls which level of security we want to achieve between client and server. CICS [online manual ]describes its usage in Figure 3.
(img)
To enable two way SSL, we need to use SSL(CLIENTAUTH) together with another attribute AUTHENTICATE(CERTIFICATE). In CICS context, a CICS user ID(consider as system ID, not used for CICS login) has to associate with the client cert. Also SSL cipher suite need to be specified in CIPHERS attribute.

We can see the ATTLSAWARE option in the SSL attribute, that will be our final target to pass SSL handshake from CICS to TCPIP. Before AT-TLS, SSL handshake in CICS is performed by CWXN transaction(Figure 4), and if you are running web service now, you must have noticed for every incoming request, CWXN is triggered first, only after client-server authentication is passed can the pipeline transaction be invoked to process the business logic.
(img)
For a setup like this, CICS transaction volume is actually doubled that of incoming requests, and you may also notice the total CPU time used by CWXN over GCP is remarkable. With the help of crypto cards, this part of CPU consumption can be relieved from GCP.

## Implementation

To implement hardware encryption/decryption using crypto cards, you need the IBM hardware security module(HSM), the latest model is [CEX7S(4769)] PCIe Cryptographic Coprocessor(Figure 5).  The card has 3 working modes:
- Common Cryptographic Architecture (CCA)
- IBM Enterprise PKCS #11 (EP11)
- Accelerator mode for offload of computer intensive operations in clear key mode
	(img)
As a mainframe network or CICS expert or as performance specialist, you may want to approach your z hardware engineer to make sure your shop do have the necessary crypto card and the card can be configured as accelerator mode. You would also need the Integrated Cryptographic Service Facility (ICSF) component on z/OS that provides access to the IBM Z CEX7S cryptographic hardware feature.

Before z15, one crypto card can only be configured as one mode, meaning if you set the card to PKCS#11 for pervasive encryption then you cannot use it to accelerate SSL encryption. But seems IBM has heard customer’s feedback (or complain), crypto card with z15 has a feature to set dual modes for one card without comprising its capacity (you may confirm with your z account manager if the feature is available in your country). Without a doubt, it is the correct move to maximise the usage of hardware crypto asset for customer.

Now assuming you have all the prerequisites ready: [TELNET running on AT-TLS], crypto cards (at least two) can be configured as accelerator mode and CICS web service running on two way SSL (CICS 5.3 and above required to enable ATTLS aware), now let’s begin our million dollar optimisation!

### Now we have our materials for the feast, let’s start cooking.
(img)
We can implement it in two phases(Figure 6):
1. Enable crypto cards as accelerator mode - this will offload TELNET and CICS CWXN SSL encryption to crypto card, but CWXN will still be triggered with minimum CPU usage. We expect 95% of SSL/AT-TLS CPU saving from GCP to crypto card.

3. Enable AT-TLS for CICS - this is to change CICS TCPIPService to ATTLSAWARE and define policy in TCPIP PAGENT. It will suppress most CWXN transaction in CICS because SSL handshake is now handled by crypto cards as well. And it will achieve another 5% of saving by pushing the optimisation to ultimate stage. The removal of CWXN transaction reduces the number of TCB switches in the user transaction. CICS SSL support requires several task switches to and from S8 TCBs, but AT-TLS does not have this requirement(fn). With combination of AUTHENTICATE(CERTIFICATE) and SSL(ATTLSAWARE), the RACF/ACF2 userid associated with client certificate will be passed to CICS. And you can remove the CIPHER suite in TCPIPService, and let it controlled by AT-TLS policy parameter “TTLSCipherParms CICSCipherParms”.

Phase 1 is purely hardware change (refresh TCPIP policy might be required), it won’t have underlaying implications to OS and middleware - so called application transparent. Whereas phase 2 involves TCPIP and CICS changes, a comprehensive QA on web service or API connections should be preformed. A two phase implementation can isolate issue more effectively but of course requires two change cycles. You may consider to merge them in one go if that suits you the best.

### Variation
Even though if you do not have or can not have crypto cards soon, it might be still worthy to migrate CICS SSL to AT-TLS, according to IBM CICS 5.3 benchmark(fn), AT-TLS uses about 24% less CPU than the CICS/SSL configuration(Figure 7).
 (img)
While waiting for the crypto cards, you can consider to migrate CICS SSL to AT-TLS first.

## How do we measure the MIPS cost and saving?
To measure how much SSL/TLS costs your organisation, we can measure it from TELNET over TCPIP and CICS separately. First, define WLM report class for TCPIP stack which runs the TELNET with SSL, also define another WLM report class for the CICS regions which handles the CICS web service SSL handshake (where CWXN transaction takes place and where you define the TCPIPService with the listening port), and don’t forget report class for the 3270 owning region.  WLM report class has to be defined before we implement the offload, because we need to measure before and after the optimisation to quantify the cost saving.

After report classes for TCPIP and CICS are defined, we can run RMF WLM report to get the CPU utilisation in %. As I have described in my previous article — \<Break the Myth of MIPS per TPS of CICS - A New Approach to Mainframe Online Workload Capacity Planning\>:

General CP CPU% is converted to MIPS for each 15 minutes interval using:

**MIPS = CPU of APPL% x Total MIPS / Number of CPU**

And use the similar SAS or other script, we can calculate the MIPS usage for SSL/TLS for TELNET and CICS. Surprisingly or not, if you have thousands of 3270 users and millions of web service per day, the MIPS used to secure your online data in-flight encryption can be impressive, but it worths every penny of it because no company can afford to lose or leak such data.

So to quantify the saving, please collect TCPIP CPU APPL% and web service +3270 CICS CPU APPL% from RMF report, we also need the CICS transaction per second (TPS) for same 15 mins interval. Then convert the CPU APPL% into MIPS for better understanding for wider range of audience. It is best to collect all the data one or two month prior to the phase one implementation date, all the way till one month after the phase two implementation. To make the tangible saving intuitive, I use Tableau to draw CICS TPS vs total MIPS of CICS and TCPIP(Figure 8).

(img)
On the horizontal we have sum of TPS for 3270 CICS or TOR for traditional TELNET sign-on and TPS for web service CICS. From vertical we can see the combination of MIPS usage for the mentioned CICS and TCPIP address space. Each data point represents the average TPS and MIPS during one 15mins interval. In this chart, orange points are before SSL offload where encryption runs in general CP, grey points are phase one implementation where SSL encryption is taken over by crypto cards. The MIPS saving is very obvious which IT managers can hardly ignore. The blue points are phase two where CWXN transaction no longer required to perform SSL handshake inside CICS.

Now with the help of HSM, heavy SSL/TLS traffic in z/OS can run fully AT-TLS mode without any CPU overhead.

## Summary
Application Transparent Transport Layer Security (AT-TLS) can be used to create secure socket sessions on behalf of CICS, it provides encryption and decryption of data based on policy statements that are coded in the TCPIP Policy Agent. Naturally TELNET as part of z/OS communication server, it can use AT-TLS in TCPIP too. Leveraging IBM crypto card for z, much of the AT-TLS workload can be offloaded from general CP to HSM, hundreds of MIPS can be saved from day to day operation, which will help organisations to reduce TCO of mainframe hardware and software. In this turbulence time with unpredictable economy growth, a predictable million dollars of saving in 3-5 years run is something one should put on top of their organisation’s priority list.

## About the author
Ming Lu is principle engineer at Central Provident Fund Board based at Singapore. He is leading mainframe system engineering team and responsible for mainframe OLTP related technology and performance. He delivers continuous modernisation and continuous optimisation on mainframe platform, he also has interests in data engineering and analytic.

He can be contacted via: Lu\_\_ming@cpf.gov.sg
