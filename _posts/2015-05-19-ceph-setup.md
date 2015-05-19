---
title: Ceph测试集群搭建
---

这里整理一篇从源码编译的测试集群搭建方法。

1.编译
---
[http://www.quts.me/2015/02/23/Ceph-First.html#centos]({{site.baseurl}}/2015/02/23/Ceph-First.html#centos)

2.部署
---
* monitor
[http://www.quts.me/2015/03/02/ceph-deploy.html#monitor]({{site.baseurl}}/2015/03/02/ceph-deploy.html#monitor)

其中，主要步骤

    * 配置文件相关：2，3，4，9
    * 集群准备：6，7，8
    * 启动：11

测试集群的可以省略密钥的配置。

* osd
[http://www.quts.me/2015/03/02/ceph-deploy.html#osd]({{site.baseurl}}/2015/03/02/ceph-deploy.html#osd)

把monitor的配置文件同步过来后，OSD可以按简单模式配置，启动参照复杂里的[11]


可能遇到的问题
---

1.无法访问monitor
关闭防火墙，centos 6 是iptable，centos 7是firewall 
systemctl stop firewalld.service
注意，这只是临时关闭，重启后会重新启动。

2.sudo找不到命令
是因为sudo的path被重置了，如下方法修改
sudo visudo
Defaults env_reset 改成 Defaults !env_reset
在~/.bashrc 添加alias sudo='sudo env PATH=$PATH'

3.sudo ceph -s找不到python依赖
也是因为路径重置了，一劳永逸的方法，把ceph/src/pybind的内容复制到Python系统路径中。

sudo cp ~/ceph/src/pybind /usr/lib/python2.7/site-packages




    
最后，good luck！