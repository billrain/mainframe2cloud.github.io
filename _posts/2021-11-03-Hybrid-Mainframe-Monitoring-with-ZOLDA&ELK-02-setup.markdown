---
layout: post
title:  "Hybrid Mainframe Monitoring with ZOLDA and ELK part2 Setup"
author: ming
categories: [ Mainframe, ELK, cloud ]
tags: featured
image: https://1.cms.s81c.com/sites/default/files/2021-08/Image-1.png
---
In the previous [article](https://mainframe2cloud.com/Hybrid-Mainframe-Monitoring-with-ZOLDA&ELK/) I share the high level overview of ZOLDA with data analytic platforms. The entire solution requires ZOLDA installation on system z and ELK on distributed like Win/Linux. The following covers SMPE installation and customisation of ZOLDA on z/OS and simple ELK config on Linux.

# ZOLDA Installation
Follow the standard SMPE installation to RECEIVE and APPLY the FMID for ZLDA(z Log Data Analytics) and ZCDP(z Common Data Provider) from CBPDO order, please note they were separate products and merged into ZOLDA(z Operation Log and Data Analytics), you will need to RECEIVE and APPLY their FMID separately, meaning 2 RIMLIB to UNZIP in the first place. Together 4 zFS mountpoints will be created, 3 for ZCDP and 1 for ZLDA.

    /usr/lpp/IBM/zcdp/v5r1m0                     
    /usr/lpp/IBM/zcdp_liberty/v5r1m0
    /var/local/CDPServer             
    /usr/lpp/IBM/zoa/v5r1m0                                 

APF authorise the HLQ.SHBOLOAD & SHBOLLST LOADLIB and add HLQ.SHBOLQA to system LPA LIST. A few SMF exits are required for STC/JOB to pass data to ZOLDA collectors, depending on your SMF setting, add exit for SYS.IEFU86, SYSSTC.IEFU86, or SYSJES2.IEFU86 if you have SYSJES2 defined in SMFPRMxx, and CNZ_MSGTOSYSLOG.

You can complete above with updated PROGxx, LPALSTxx in system parmlib and IPL, or perform dynamic changes with below cmds:

    SETPROG APF,ADD,DSNAME=HLQ.SHBOLOAD,SMS
    SETPROG LPA,ADD,MODNAME=HBOSYSG,DSNAME=HLQ.SHBOLPA
    SETPROG EXIT,ADD,EXITNAME=CNZ_MSGTOSYSLOG,MODNAME=HBOSYSG
    D PROG,EXIT,EXITNAME=CNZ_WTOMDBEXIT


# ZOLDA Customisation

Apply 2 STC IDs for HBOCFGA(config tool angel), HBOCFGT(confit tool) under ID HBOSTCID.
Apply 3 STC IDs for HBODSPRO(data streamer), HBOPROC(log forwarder), HBOSMF(system data engine) under the ID HBOLGF.
Optional: Apply STC ID for GPMSERVE(RMF DDS) and HBOREC(data receiver).

Apply 3 non-interactive user IDs: HBOSTCID, HBOLGF, HBOGUEST.
Apply one SSL cert for config tool and add into its keyring.
Apply RACF/ACF2 accesses according to the user guide and additional access if your TCPIP is protected by NETACCESS.

## Config Tool
1. Run /usr/lpp/IBM/zcdp/v5r1m0/UI/LIB/defracf.cmd, regardless your run RACF or ACF2
2. Update /var/cdp-uiconfig/cdpui.properties with site's OMVS group and ID.
3. Goto /usr/lpp/IBM/zcdp/v5r1m0/UI/LIB/, run ./savingpolicy.sh
4. Create and update HBOCFGT
5. Update JVM profile /var/local/CDPServer/servers/cdp_ui_server/server.xml with your keyring
6. Create and update HBOCFGA
7. Start HBOCFGA first, followed by HBOCFGT (always start angel process before Liberty)

In HBOCFTG STG log:

    CWWKT0016I: Web application available (default_host): https://xxx.xx.xx.xx:17977/cdp/       
    CWWKZ0001I: Application CDPUIServer started in 17.780 seconds.

In Liberty messages.log:

    This server is connected to the HBOCFGA angel process.    
    Authorized service group KERNEL is available.             
    Authorized service group SAFCRED is available.                                       

Then you should be able to access the config tool web GUI via https://xxx.xx.xx.xx:17977/cdp/ , where xxx is your mainframe host IP. After you create the first policy using the web tool, a few files are created under /var/local/CDPServer/cdpConfig/ like myPolicy.policy, where will be used in subsequent steps.

## Data Streamer
1. Create and update HBODSPRO with the policy to be used, change port, CDP_HOME where necessary
2. CDP_HOME is the dir where cached data is stored when pipeline is not Online
3. Start HBODSPRO

## Log Forwarder
1. Create and update HBOPROC
2. Create /var/local/CDPServer/zlfconfig, /var/local/CDPServer/zlfwork, /var/local/CDPServer/zlflog
3. /var/local/CDPServer/cdpConfig/mypolicy.zlf.conf is created when you create the policy using the web tool, copy it
to /var/local/CDPServer/zlflog/xxxx.xxxx.zlf.config
4. Copy /var/local/CDPServer/cdpConfig/mypolicy.config.properties to /var/local/CDPServer/zlfconfig/xxxx.xxxx.config.properties
5. Ensure /usr/lpp/IBM/zcdp/* is readable by the group of HBO* ID
6. Start HBOPROC

## System Data Engine
1. Create and update HBOSMF, the /var/local/CDPServer/cdpConfig/mypolicy.sde is created with web config tool as well
2. Copy /usr/lpp/IBM/zcdp/v5r1m0/SDE/samples/log4j.properties to /var/local/CDPServer/cdpConfig/log4j.properties
3. Start HBOSMF

If you are lucky, all the 5 STC of ZOLDA are up running, congratulations!

********

# ELK setup
1. Follow the public resources to install ELK on Linux or Mac
2. You may need to issue this:

    sudo mount -o remount,exec /tmp

3. Ensure you can access Kibana after setup from http://linux.host.ip:5601/app/home#/
4. Linux cmds to check status of ELK:

```
     ps -ef | grep logstash
     netstat -a -n | grep 8080
     netstat -a -n | grep 9600
     ps -ef | grep elasticsearch
     netstat -a -n | grep 9200
     netstat -a -n | grep 9300
     curl -XGET 'linux.host.ip:9200/?pretty'
     curl -XGET 'http://elastic:password@linux.host.ip:9200/?pretty'
     curl -XGET 'linux.host.ip:9200/_cluster/health'
     ps -ef | grep kibana
     ps -ef | grep node
     netstat -a -n | grep 5601
     curl -I http://linux.host.ip:5601
```

*******

# ZOLDA integration with ELK
IBM ZOLDA comes with 2 types of conf files for ELK:
* Curated for IBM pre-defined subset of SMF, set of ELK indexes, templates and dashboards
* Raw for all SMF metrics without pre-defined data mapping (index template in kibana)

Make sure you have the 3 zip files for ELK conf from ZOLDA.

## Install the Curated plugin for ELK
1. Make sure Kibana is up
2. Make sure Python is installed
3. Unzip xx to /elk/ZLDA-ELASTIC-5.1.0.0
4. Goto /elk/ZLDA-ELASTIC-5.1.0.0, and run python setup.py

## Deploy ingestion kit for Curated
1. Unzip ZLDA-IngestionKit-curated-5.1.0.0.zip to /elk/zlda-config-curated
2. Update Q_elasticsearch.conf with Logstash IP and port, add user and password if min security is configured for elk

## Deploy ingestion kit for Raw
1. Unzip ZLDA-IngestionKit-raw-5.1.0.0.zip and only copy selected conf files to /elk/zlda-config-raw
2. Update Q_CDPz_Elastic.conf with Logstash IP and port, add user and password if min security is configured for elk


## Configuring a pipeline for Logstash
1. For starting, try Curated pipeline first because you can play with those IBM supplied dashboards
2. Update /elk/logstash-7.15.0/config/pipelines.yml

```
- pipeline.id: zlda-config-curated
  path.config: "/elk/zlda-config-curated/*.conf"
```

3. Restart Logstash

If you are lucky again, your first mainframe-ELK data pipleine is ONLINE! SYSLOG and SMF can be visualised in Kibana.
