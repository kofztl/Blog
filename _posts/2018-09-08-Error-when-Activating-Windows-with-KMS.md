---
title: Error "Product key not found" or "Product not found" when Activating Windows with KMS  
tags:  
  - Windows Server    
categories: KnowledgeBase  

---
## Symptom
`slmgr /ato` with error: **Product not found**.  
  
`slmgr /dlv` with error: **Product key not found**.  


## Resolution
### Install License Key

Get KMS client setup keys from [Microsoft](https://docs.microsoft.com/en-us/windows-server/get-started/kmsclientkeys).  
Here are some windows server KMS client setup keys:  
> Windows Server 2008 R2 Enterprise: 489J6-VHDMP-X63PK-3K798-CPX3Y  
> Windows Server 2012 R2 Standard:   D2N9P-3P6X9-2R39C-7RTCD-MDVJX  
> Windows Server 2016 Standard:      WC2BQ-8NRM3-FDDYY-2BFGV-KHKQY  

Install setup key with `slmgr /ipk XXXXX-XXXXX-XXXXX-XXXXX-XXXXX`  


### Point to your KMS server  

`slmgr /skms XXX.XXX.XXX.XXX`

### Active windows  

`slmgr /ato`
