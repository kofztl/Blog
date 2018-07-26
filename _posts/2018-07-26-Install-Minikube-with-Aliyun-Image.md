---
title: Install Minikube with Aliyun Image  
tags: Kubernetes  
categories: Blog  

---

## Preparation  

* Install [Kubectl](https://kubernetes.io/docs/tasks/kubectl/install/?spm=a2c4e.11153940.blogcont221687.19.7dd57733RN1f0I)  
* Install Virtual Box 

## Installation  

Get Mac OSX image  
`curl -Lo minikube http://kubernetes.oss-cn-hangzhou.aliyuncs.com/minikube/releases/v0.25.2/minikube-darwin-amd64 && chmod +x minikube && sudo mv minikube /usr/local/bin/ ` 

Build local kubernetes environment with virtual box  
`minikube start --registry-mirror=https://registry.docker-cn.com`  

Open Kubernetes dashboard  
`minikube dashboard`  
![Kubernetes Dashboard](http://pc58ypabw.bkt.clouddn.com/Jietu20180726-154219@2x.jpg)  

## Test  

Run a Nginx Pod  
`kubectl run hello-world --image=nginx --port=80`  
![](http://pc58ypabw.bkt.clouddn.com/Jietu20180726-154800@2x.jpg)  

Expose Nginx Service  
`kubectl expose deployment hello-world --type=NodePort`  
![](http://pc58ypabw.bkt.clouddn.com/Jietu20180726-154821@2x.jpg)  

Get Pod and Service status  
`kubectl get pod`  
![](http://pc58ypabw.bkt.clouddn.com/Jietu20180726-154859@2x.jpg)  
`kubectl get service`  
![](http://pc58ypabw.bkt.clouddn.com/Jietu20180726-154916@2x.jpg)  

Test with browser  
![](http://pc58ypabw.bkt.clouddn.com/Jietu20180726-154941@2x.jpg)  


