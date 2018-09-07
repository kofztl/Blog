---
title: Failover Cluster Resource Failed by Heartbeat Issue in Windows Server 2012 R2  
tags:  
  - Windows Server    
categories: KnowledgeBase  

---
## Symptom
Event 4201: isatap interface is no longer active.  
![](http://pc58ypabw.bkt.clouddn.com/4201.jpg)  
Event 1135: cluster node was removed from the active failover cluster membership.  
![](http://pc58ypabw.bkt.clouddn.com/1135.jpg). 

## Resolution
### Edit Registry

Location: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\services\TCPIP6\Parameters  
KeyType:  DWORD (32-bit)  
KeyName:  DisabledComponents  
KeyValue: FF  
[Reference Link](https://na01.safelinks.protection.outlook.com/?url=https%3A%2F%2Fsupport.microsoft.com%2Fzh-cn%2Fhelp%2F929852%2Fhow-to-disable-ipv6-or-its-components-in-windows&data=02%7C01%7Cyinhc%40microsoft.com%7Cd96e39c3b2244b9c275608d613bf5eb4%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636718110019403358&sdata=BNtbc7ZvTj1n22ysywSTiPPqR6HQ7pBC%2B513AzP2e6U%3D&reserved=0)  
![](http://pc58ypabw.bkt.clouddn.com/disabledcomponents.jpg)  


### Modify Failover Cluster Parameters  

```  
Import-module failoverclusters
(get-cluster).SameSubnetThreshold = 10
(get-cluster).CrossSubnetThreshold = 10
(get-cluster).CrossSubnetDelay = 4000
(get-cluster).SameSubnetDelay = 2000 
```  
Verify configurations with `get-cluster | fl *subnet*`

### Disable TCP/IP Features  

```  
netsh int tcp set global chimney=disabled
netsh int tcp set global netdma=disabled
netsh int ip set global taskoffload=disabled  
```  
[Reference Link](https://na01.safelinks.protection.outlook.com/?url=http%3A%2F%2Fsupport.microsoft.com%2Fkb%2F951037&data=02%7C01%7Cyinhc%40microsoft.com%7Cd96e39c3b2244b9c275608d613bf5eb4%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636718110019403358&sdata=WVQFxTmKExpOFZKLO3h%2F5lTcED6XmmskHiQQt2h34X4%3D&reserved=0)
