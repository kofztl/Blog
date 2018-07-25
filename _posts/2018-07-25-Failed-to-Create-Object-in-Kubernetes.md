---
title: Failed to Create Object in Kubernetes  
tags: Kubernetes  
categories: KnowledgeBase  

---

## Symptom

When you create object such as pod, service, replicationcontroller in Kubernetes, you may get error information as below:

> No API token found for service account "default", retry after the token is automatically created and added to the service account

## Resolution

### Disable service account authentication from Kubernetes admission control

1. Edit `/etc/kubernetes/apiserver`, delete **ServiceAccount** item in `KUBE_ADMISSION_CONTROL="--admission-control=NamespaceLifecycle,NamespaceExists,LimitRanger,SecurityContextDeny,ServiceAccount,ResourceQuota"
`  
2. Restart Kubernetes service in Master.  
`systemctl restart etcd kube-apiserver kube-controller-manager kube-scheduler
`

### Generate service account ssl key

1. Generate ssl key  
`openssl genrsa -out /etc/kubernetes/serviceaccount.key 2048`  
2. Edit `/etc/kubenetes/apiserver`, add `KUBE_API_ARGS="--service_account_key_file=/etc/kubernetes/serviceaccount.key"`  
3. Edit `/etc/kubernetes/controller-manager`, add `KUBE_CONTROLLER_MANAGER_ARGS="--service_account_private_key_file=/etc/kubernetes/serviceaccount.key"`  
4. Restart Kubernetes service in Master.  
`systemctl restart etcd kube-apiserver kube-controller-manager kube-scheduler
`  
