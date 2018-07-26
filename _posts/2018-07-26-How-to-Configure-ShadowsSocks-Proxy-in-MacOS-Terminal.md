---
title: How to Configure Shadowsocks Proxy in MacOS Terminal  
tags: ShadowSocks  
categories: KnowledgeBase  

---

## 仅在当前Terminal窗口生效

`export ALL_PROXY=socks5://127.0.0.1:12345`  

## 全局配置
{% highlight javascript linenos %}  
vi ~/.bash_profile	# 编辑配置文件 
alias setproxy="export ALL_PROXY=socks5://127.0.0.1:12345" 
alias unsetproxy="unset ALL_PROXY" 
{% endhighlight %}  

{% highlight javascript linenos %}  
source ~/.bash_profile	# 立即生效 
setproxy	# 开启代理 
unsetproxy	# 关闭代理 
{% endhighlight %}  
