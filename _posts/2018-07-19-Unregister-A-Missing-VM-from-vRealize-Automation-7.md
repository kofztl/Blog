---
title: Unregister A Missing VM from vRealize Automation 7  
tags: 
- VMware  
- vRealize Automation     
categories: 
- kb  

---

### Purpose

This article provides steps to unregister a missing virtual machine from vRealize Automation 7.

You may want to do this because a virtual machine deployed by vRealize Automation has been deleted from vCenter Server and status of this virtual machine changed from **on** to **missing**.


More info: [Removing a virtual machine from vRealize Automation 7.x using Cloud Client (2144269)](https://kb.vmware.com/s/article/2144269)

### Resolution

1. Log in vRealize Automation platform, and select **Machines** under **Items** tab. 
2. Look for virtual machines in **Missing** status. 
3. Select target virtual machine and click **Actions**, choose **Unregister**.
![Unregister a virtual machine](http://pc58ypabw.bkt.clouddn.com/Jietu20180719-175609.jpg)
4. Click **Submit** to confirm.  
![Confirm](http://pc58ypabw.bkt.clouddn.com/Jietu20180719-181253.jpg)  
5. Finally, missing virtual machine has disappeared in vRealize Automation.

