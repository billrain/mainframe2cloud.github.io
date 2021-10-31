---
layout: post
title:  "Hybrid Mainframe Monitoring with ZOLDA and ELK part2 Setup"
author: ming
categories: [ Mainframe, ELK, cloud ]
tags: featured
image: https://1.cms.s81c.com/sites/default/files/2021-08/Image-1.png
---
In the previous [article](https://mainframe2cloud.com/Hybrid-Mainframe-Monitoring-with-ZOLDA&ELK/) I share the high level overview of ZOLDA with data analytic platforms. The entire solution requires ZOLDA installation on system z and ELK on distributed like Win/Linux. The following covers SMPE installation and customisation of ZOLDA on z/OS and ELK simple ELK config on Linux.

# ZOLDA Installation
Follow the standard SMPE installation to RECEIVE and APPLY the FMID for ZLDA and ZCDP from CBPDO order, please note they were separate products and merged into ZOLDA, you will need to RECEIVE and APPLY their FMID separately, meaning 2 RIMLIB to UNZIP in the first place. Together 4 zFS mountpoints will be created, 3 for ZCDP and one for ZLDA.

APF authorise the HLQ.SHBOLOAD/SHBOLLST LOADLIB and add HLQ.SHBOLQA to system LPA LIST. A few SMF exits are required for STC/JOB to pass data to ZOLDA collectors, depending on your SMF setting, add exit for SYS.IEFU86, SYSSTC.IEFU86, or SYSJES2.IEFU86 if you have SYSJES2 defined in SMFPRMxx, and CNZ_MSGTOSYSLOG.

You can complete above with updated sysparm and IPL, or perform dynamic changes with below cmds:

    SETPROG APF,ADD,DSNAME=HLQ.SHBOLOAD,SMS
    SETPROG LPA,ADD,MODNAME=HBOSYSG,DSNAME=HLQ.SHBOLPA
    SETPROG EXIT,ADD,EXITNAME=CNZ_MSGTOSYSLOG,MODNAME=HBOSYSG
    D PROG,EXIT,EXITNAME=CNZ_WTOMDBEXIT


# ZOLDA Customisation



# ELK setup



# ZOLDA integration with ELK
