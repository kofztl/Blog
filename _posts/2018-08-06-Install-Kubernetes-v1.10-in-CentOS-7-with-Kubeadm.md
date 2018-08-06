---
title: Install Kubernetes v1.10 in Centos 7 with Kubeadm    
tags: Kubernetes  
categories: Blog  

---

## 准备工作  
  
* 准备3-4台CentOS 7.2虚拟机 

## 系统配置  

关闭SELinux和防火墙  
```  
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config  
setenforce 0  
systemctl stop firewalld && systemctl disable firewalld  
```



