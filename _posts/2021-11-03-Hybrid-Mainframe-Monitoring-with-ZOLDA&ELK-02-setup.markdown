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

Then you should be able to access the config tool web GUI via https://xxx.xx.xx.xx:17977/cdp/ , where xxx is your mainframe host IP.

# ELK setup



# ZOLDA integration with ELK
