---
title: ceph性能测试
---

ceph与fio的结合见[Ceph Performance Analysis: fio and RBD](https://telekomcloud.github.io/ceph/2014/02/26/ceph-performance-analysis_fio_rbd.html)。这是官方给出的样例。

fio的安装
---
centos下，从源码编译安装

{% highlight bash %}
wget http://brick.kernel.dk/snaps/fio-2.2.8.tar.gz
tar -zxvf fio-2.2.8.tar.gz
#安装依赖包,librbd用于连接ceph测试
sudo yum install -y libaio-devel zlib-devel librbd1-devel

#其中，librbd1-devel提供fio rbd测试接口，安装需要配置ceph的源
sudo vim /etc/yum.repos.d/ceph.repo
#粘贴如下内容，其中hammer是当前release版本，el7表示centos7，下载源用的欧洲的，注意把gpgcheck置0，因为经常连不上
[ceph-noarch]
name=Ceph noarch packages
baseurl=http://eu.ceph.com/rpm-hammer/el7/noarch
enabled=1
gpgcheck=0
type=rpm-md
gpgkey=https://ceph.com/git/?p=ceph.git;a=blob_plain;f=keys/release.asc
#添加完后执行，sudo yum update

cd fio-2.2.8
#安装librbd后可以看到`Rados Block Device engine`选项为yes
./configure
make
sudo make install
{% endhighlight %}


测试
---

查看挂载
df -hT
查看io状态,每隔2s查看sda的扩展统计，共6次,
[iostat](http://blog.csdn.net/zhangjay/article/details/6656771)
iostat -x sda 2 6

    avgrq-sz
  发送到设备的请求的平均大小,单位是扇区.

    avgqu-sz
  发送到设备的请求的平均队列长度.
