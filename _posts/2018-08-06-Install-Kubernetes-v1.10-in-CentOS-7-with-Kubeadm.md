---
title: Install Kubernetes v1.10 in Centos 7 with Kubeadm    
tags: Kubernetes  
categories: Blog  

---

## 准备工作  
  
* 准备3-4台CentOS 7.2虚拟机  



## 系统配置（所有节点都要执行）  
  
#### 关闭SELinux和防火墙  
  
```  
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
setenforce 0
systemctl stop firewalld && systemctl disable firewalld
```
  
#### RHEL 7/CentOS 7上配置sysctl的参数防止流量绕过iptables  
```  
cat <<EOF >  /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sysctl --system
```  
  
#### 关闭系统SWAP  
```  
swapoff -a
```  
修改/etc/fstab文件，注释SWAP自动挂载  
  
## Docker安装与配置（所有节点）
  
#### 安装Docker
```  
mkdir ~/k8s
cd k8s
wget https://download.docker.com/linux/centos/7/x86_64/stable/Packages/docker-ce-selinux-17.03.2.ce-1.el7.centos.noarch.rpm
wget https://download.docker.com/linux/centos/7/x86_64/stable/Packages/docker-ce-17.03.2.ce-1.el7.centos.x86_64.rpm
yum install ./docker-ce-selinux-17.03.2.ce-1.el7.centos.noarch.rpm
yum install ./docker-ce-17.03.2.ce-1.el7.centos.x86_64.rpm
systemctl enable docker
systemctl start docker
```  

#### 配置Docker  
开启iptables filter表的FORWARD链  
编辑/lib/systemd/system/docker.service，在ExecStart=..上面加入如下内容：  
```  
ExecStartPost=/usr/sbin/iptables -I FORWARD -s 0.0.0.0/0 -j ACCEPT
ExecStart=/usr/bin/dockerd
.....  
```  
#### 配置国内镜像加速器。
因为kubeadm默认要从google的镜像仓库下载镜像，但目前国内无法访问google镜像仓库，所以需要我们配置成国内的镜像仓库，并在kubeadm init前pull下所需的镜像。  

使用阿里云镜像加速器：[阿里云容器hub](https://dev.aliyun.com/search.html)。 登录之后，进入管理中心-->镜像加速器-->操作文档，根据提示进行设置即可。  
> 注：需额外修改/etc/systemd/system/multi-user.target.wants/docker.service，找到 ExecStart= 这一行，在这行最后添加加速器地址 --registry-mirror=<加速器地址>  

#### 重启Docker服务
```  
systemctl daemon-reload && systemctl restart docker && systemctl status docker
```  

## 下载镜像  

#### Master节点  
```  
docker pull keveon/kube-apiserver-amd64:v1.10.0
docker pull keveon/kube-scheduler-amd64:v1.10.0
docker pull keveon/kube-controller-manager-amd64:v1.10.0
docker pull keveon/kube-proxy-amd64:v1.10.0
docker pull keveon/k8s-dns-kube-dns-amd64:1.14.8
docker pull keveon/k8s-dns-dnsmasq-nanny-amd64:1.14.8
docker pull keveon/k8s-dns-sidecar-amd64:1.14.8
docker pull keveon/etcd-amd64:3.1.12
docker pull keveon/flannel:v0.10.0-amd64
docker pull keveon/pause-amd64:3.1

docker tag keveon/kube-apiserver-amd64:v1.10.0 k8s.gcr.io/kube-apiserver-amd64:v1.10.0
docker tag keveon/kube-scheduler-amd64:v1.10.0 k8s.gcr.io/kube-scheduler-amd64:v1.10.0
docker tag keveon/kube-controller-manager-amd64:v1.10.0 k8s.gcr.io/kube-controller-manager-amd64:v1.10.0
docker tag keveon/kube-proxy-amd64:v1.10.0 k8s.gcr.io/kube-proxy-amd64:v1.10.0
docker tag keveon/k8s-dns-kube-dns-amd64:1.14.8 k8s.gcr.io/k8s-dns-kube-dns-amd64:1.14.8
docker tag keveon/k8s-dns-dnsmasq-nanny-amd64:1.14.8 k8s.gcr.io/k8s-dns-dnsmasq-nanny-amd64:1.14.8
docker tag keveon/k8s-dns-sidecar-amd64:1.14.8 k8s.gcr.io/k8s-dns-sidecar-amd64:1.14.8
docker tag keveon/etcd-amd64:3.1.12 k8s.gcr.io/etcd-amd64:3.1.12
docker tag keveon/flannel:v0.10.0-amd64 quay.io/coreos/flannel:v0.10.0-amd64
docker tag keveon/pause-amd64:3.1 k8s.gcr.io/pause-amd64:3.1
```

#### Node节点  
```  
docker pull keveon/kube-proxy-amd64:v1.10.0
docker pull keveon/flannel:v0.10.0-amd64
docker pull keveon/pause-amd64:3.1
docker pull keveon/kubernetes-dashboard-amd64:v1.8.3
docker pull keveon/heapster-influxdb-amd64:v1.3.3
docker pull keveon/heapster-grafana-amd64:v4.4.3
docker pull keveon/heapster-amd64:v1.4.2

docker tag keveon/flannel:v0.10.0-amd64 quay.io/coreos/flannel:v0.10.0-amd64
docker tag keveon/pause-amd64:3.1 k8s.gcr.io/pause-amd64:3.1
docker tag keveon/kube-proxy-amd64:v1.10.0 k8s.gcr.io/kube-proxy-amd64:v1.10.0
docker tag keveon/kubernetes-dashboard-amd64:v1.8.3 k8s.gcr.io/kubernetes-dashboard-amd64:v1.8.3
docker tag keveon/heapster-influxdb-amd64:v1.3.3 k8s.gcr.io/heapster-influxdb-amd64:v1.3.3
docker tag keveon/heapster-grafana-amd64:v4.4.3 k8s.gcr.io/heapster-grafana-amd64:v4.4.3
docker tag keveon/heapster-amd64:v1.4.2 k8s.gcr.io/heapster-amd64:v1.4.2
```  

## Kubernetes安装与配置（所有节点）  

#### 配置yum源并安装  
```  
cat > /etc/yum.repos.d/kubernetes.repo <<EOF
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=0
repo_gpgcheck=0
EOF
```  

#### 安装制定版本的kubeadm  
```  
yum install -y kubeadm-1.10.0
```  

#### 配置kubelet参数  
确保kubelet的配置文件/etc/systemd/system/kubelet.service.d/10-kubeadm.conf里的--cgroup-driver和docker info中的一致，一般为cgroupfs。  

#### 配置开机自动运行kubelet并启动kubelet  
```  
systemctl enable kubelet
systemctl start kubelet
```  
> 注： kubelet启动失败可暂时忽略，因为还没有初始化  

## 使用kubeadm init初始化集群（仅Master节点执行）  

#### 初始化命令  
```  
kubeadm init  --skip-preflight-checks --kubernetes-version=v1.10.0 --pod-network-cidr=10.244.0.0/16
```  
> 注： 如果执行失败可使用`kubeadm reset`进行清理。  

初始化主要过程为：
> 1.kubeadm 执行初始化前的检查  
> 2.生成 token 和证书  
> 3.生成 KubeConfig 文件，kubelet 需要这个文件与 Master 通信。  
> 4.安装 Master 组件，会从 goolge 的 Registry 下载组件的 Docker 镜像，这一步可能会花一些时间，主要取决于网络质量，如果本地已有相关镜像则会优先使用本地的。  
> 5.安装附加组件 kube-proxy 和 kube-dns  
> 6.Kubernetes Master 初始化成功  
> 7.提示后续操作  

记录join记录中的token和证书信息，后面加入Node时会用到。  

```  
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```  

#### 查看集群状态  
```  
kubectl get cs
```  

## 安装网络插件——flannel（仅Master节点执行）  

```  
$ wget https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
$ kubectl apply -f  kube-flannel.yml
clusterrole.rbac.authorization.k8s.io "flannel" created
clusterrolebinding.rbac.authorization.k8s.io "flannel" created
serviceaccount "flannel" created
configmap "kube-flannel-cfg" created
daemonset.extensions "kube-flannel-ds" created
```  
#### 安装后检查网络配置中已存在flannel网络  
```  
ip a  
```  

## 向Kubernetes中添加Node（每个Node中执行）  

```  
kubeadm join --token <token> <master-ip>:<master-port> --discovery-token-ca-cert-hash sha256:<hash>
```  

#### 获取节点状态  
```  
kubectl get nodes
```  

## 部署Dashboard插件  
#### 下载Dashboard插件  
```  
cd ~/k8s
wget https://raw.githubusercontent.com/kubernetes/dashboard/master/src/deploy/recommended/kubernetes-dashboard.yaml
```  

修改yaml配置参数，在spec中增加type: NodePort， 这样可以通过主机IP地址和对应端口访问Dashboard。  

#### 创建kubernetes-dashboard-admin.rbac.yaml  
```  
---  
apiVersion: v1
kind: ServiceAccount
metadata:
  labels:
    k8s-app: kubernetes-dashboard
  name: kubernetes-dashboard-admin
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: kubernetes-dashboard-admin
  labels:
    k8s-app: kubernetes-dashboard
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: kubernetes-dashboard-admin
  namespace: kube-system
```  

#### 创建Pod  
```  
kubectl create -f kubernetes-dashboard.yaml
kubectl create -f kubernetes-dashboard-admin.rbac.yaml
```  

#### 查看分配到的NodePort  
```  
kubectl get svc,pod --all-namespaces | grep dashboard
```  

#### 部署Heapster插件  
安装Heapster为集群添加使用统计和监控功能，为Dashboard添加仪表盘。  
```  
mkdir -p ~/k8s/heapster
cd ~/k8s/heapster
wget https://raw.githubusercontent.com/kubernetes/heapster/master/deploy/kube-config/influxdb/grafana.yaml
wget https://raw.githubusercontent.com/kubernetes/heapster/master/deploy/kube-config/rbac/heapster-rbac.yaml
wget https://raw.githubusercontent.com/kubernetes/heapster/master/deploy/kube-config/influxdb/heapster.yaml
wget https://raw.githubusercontent.com/kubernetes/heapster/master/deploy/kube-config/influxdb/influxdb.yaml
kubectl create -f ./
```  

#### 查看kubernete-dashboard-admin的token  
```  
kubectl -n kube-system get secret | grep kubernetes-dashboard-admin
```  

#### 使用Firefox或Safari通过填写token的方式访问Dashboard  
![](http://pc58ypabw.bkt.clouddn.com/Jietu20180806-210614@2x.jpg)  

