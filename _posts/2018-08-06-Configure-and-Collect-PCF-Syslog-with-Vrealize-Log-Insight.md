---
title: Configure and collect PCF syslog with vRealize Log Insight    
tags:  
- Pivotal Cloud Foundry  
- VMware  
- vRealize Log Insight    
categories: Blog  

---

## Install Content Pack for Pivotal Cloud Foundry  
  
Log in to vRealize Log Insight with administrator. In the **Content Pack Marketplace**, find **Pivotal - Cloud Foundry** content pack and install it.

![Content Pack Marketplace](http://pc58ypabw.bkt.clouddn.com/PCF_Content_Pack.jpg)  


## Configure VIP for PCF datacollector   

Follow content pack setup instructions, create a virtual IP address for PCF syslog collector.

![Setup Instructions](http://pc58ypabw.bkt.clouddn.com/PCF_Setup_Instructions.jpg)  
![VIP Configuration](http://pc58ypabw.bkt.clouddn.com/Configure_VIP_for_PCF.jpg)

## Configure syslog in PCF tiles

Log in to Pivotal Ops Manager with administrator, configure each tile which you want to forward syslog to vRealize Log Insight with virtual IP and port 514.

![Tile Syslog Configuration](http://pc58ypabw.bkt.clouddn.com/syslog_in_tile.jpg)


## Use vRealize Log Insight to analyze PCF syslog  

There are several features by default:

* Key events in 6 dashboards
* Access 5 alerts to notify administrators of key events
* Abstract and analyze logs with 11 unique fields

![PCF Syslog Dashboard](http://pc58ypabw.bkt.clouddn.com/PCF_dashboard.jpg)  

![Syslog Interactive Analytics](http://pc58ypabw.bkt.clouddn.com/PCF_log_example.jpg)  



