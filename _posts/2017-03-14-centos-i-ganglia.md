---
title: centos install ganglia
date: 2017-09-14
categories: devops
tags: [devops,linux]
---

> 网上搜到一个contos7的部署指南折腾了好几天最后在centos6上搞定了。

## 介绍
Ganglia是UC Berkeley发起的一个开源集群监视项目，设计用于测量数以千计的节点。Ganglia的核心包含gmond、gmetad以及一个Web前端。主要是用来监控系统性能，如：cpu 、mem、硬盘利用率、 I/O负载、网络流量情况等，通过曲线很容易见到每个节点的工作状态，对合理调整、分配系统资源，提高系统整体性能起到重要作用。

每台计算机都运行一个收集和发送度量数据的名为 gmond 的守护进程。接收所有度量数据的主机可以显示这些数据并且可以将这些数据的精简表单传递到层次结构中。正因为有这种层次结构模式，才使得 Ganglia 可以实现良好的扩展。gmond 带来的系统负载非常少，这使得它成为在集群中各台计算机上运行的一段代码，而不会影响用户性能。所有这些数据多次收集会影响节点性能。网络中的 “抖动”发生在大量小消息同时出现时，可以通过将节点时钟保持一致，来避免这个问题。 gmetad可以部署在集群内任一台节点或者通过网络连接到集群的独立主机，它通过单播路由的方式与gmond通信，收集区域内节点的状态信息，并以XML数据的形式，保存在数据库中。 由RRDTool工具处理数据，并生成相应的的图形显示，以Web方式直观的提供给客户端。

### 1.安装EPEL源
``` bash
	rpm -Uvh http://dl.fedoraproject.org/pub/epel/6/i386/epel-release-6-8.noarch.rpm
	sudo yum install epel-release
```

### 2.安装依赖
``` bash
	sudo yum -y install automake autoconf libtool ncurses-devel libxslt groff pcre-devel pkgconfig
	sudo yum -y install httpd-devel php automake autoconf libtool ncurses-devel libxslt groff pcre-devel pkgconfig
``` 

### 3.在监控主节点安装ganglia-gmetad 和 ganglia-web

``` bash
yum search ganglia
sudo yum install ganglia-gmetad.x86_64 ganglia-web.x86_64

结果出错了：
Error: Package: ganglia-3.7.2-2.el6.x86_64 (epel)
	           Requires: libpcre.so.0()(64bit)
Error: Package: ganglia-gmetad-3.7.2-2.el6.x86_64 (epel)
	           Requires: libpcre.so.0()(64bit)
```
这个折腾了很长时间也没解，最后没办法换到centos6上了。

rpm -qa | grep ganglia


### 4.配置gmetad
``` bash
vi /etc/ganglia/gmetad.conf 
data_source "集群名称" gmetad主机名或者IP
修改Apache配置
vi /etc/httpd/conf.d/ganglia.conf  
文件内容Deny from all 改为 Allow from all  

#设置开机自启
sudo chkconfig --add gmetad  
chkconfig --add gmond  
chkconfig --add httpd 
#修改hosts、关闭防火墙、关闭SELINUX
``` 

### 5.安装启动监控节点的后台进程gmond
``` bash
yum search ganglia
yum install -y ganglia-gmond.x86_64

#配置gmond
vi /etc/ganglia/gmond.conf  

cluster {  
  name = "hadoop cluster" --- cluster名称,是你在gmetad.conf里配置的data_source名称  
  owner = "unspecified"  
  latlong = "unspecified"  
  url = "unspecified"  
}  
  
udp_send_channel {  
  #mcast_join = 239.2.11.71 --- 组播  
  host = localhost          --- 单播 gmeta主机的ip/主机名lboenMasterTest  
  port = 8649  
  ttl = 1  
}  
  
udp_recv_channel {  
  #mcast_join = 239.2.11.71  
  port = 8649  
  bind = localhost        --- 本机的ip/hostname  
}  
```

### 6.配置开机自启
``` bash
chkconfig –add gmond
``` 

### 7.启动服务
sudo service gmetad start
sudo service gmond start
sudo service httpd start
service gmetad status     ---验证是否启动成功
service gmond status

### 6.测试站点

http://gmetadHost/ganglia

---
## 端口的问题解决:
``` bash
	[bblu@cent6 html]$ netstat -an|grep 80
	tcp        0      0 :::80                       :::*                        LISTEN      

	unix  2      [ ]         DGRAM                    11080  

	unix  3      [ ]         STREAM     CONNECTED     10080  /var/run/dbus/system_bus_socket

	[bblu@cent6 html]$ sudo tail -f /var/log/messages

	Feb 15 19:48:57 cent6 /usr/sbin/gmond[1317]: [PYTHON] Can't call the metric handler function for [tcp_attemptfails] in the python module [netstats].#012

	#iptable的命令，并且把tcp的80端口设置成允许任何IP都可以访问：

	iptables -I INPUT -p TCP --dport 80 -j ACCEPT  
```