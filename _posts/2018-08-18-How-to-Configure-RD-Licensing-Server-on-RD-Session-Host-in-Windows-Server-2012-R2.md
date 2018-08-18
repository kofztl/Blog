---
title: How to Configure RD Licensing Server on RD Session Host in Windows Server 2012 R2  
tags:  
  - Windows Server    
categories: KnowledgeBase  

---

### Configure RD Licensing Server

```  
$obj = gwmi -namespace "Root/CIMV2/TerminalServices" Win32_TerminalServiceSetting
$obj.SetSpecifiedLicenseServerList("Your Licensing Server FQDN")  
```  

### Verify RD Licensing Server Configuration  

```  
$obj = gwmi -namespace "Root/CIMV2/TerminalServices" Win32_TerminalServiceSetting
$obj.GetSpecifiedLicenseServerList()  
```  

### Change Licensing Mode  

```  
$obj = gwmi -namespace "Root/CIMV2/TerminalServices" Win32_TerminalServiceSetting
$obj.ChangeMode(value)  
```  
> PS: Value = 2(Per Device)/4(Per User)

### Verify Licensing Mode  

```  
$obj = gwmi -namespace "Root/CIMV2/TerminalServices" Win32_TerminalServiceSetting
$obj.LicensingType
$obj.LicensingName  
```  
