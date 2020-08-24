
参考地址：https://www.cnblogs.com/wsy0202/p/11088394.html

Netdata安装和使用（Linux 性能实时监测工具）
Netdata 是一款 Linux 性能实时监测工具.。以web的可视化方式展示系统及应用程序的实时运行状态（包括cpu、内存、硬盘输入/输出、网络等linux性能的数据）。


Netdata安装（CentOS）：

1.安装Netdata需要的基本编译环境安装：

```
[root@localhost ~]# yum install zlib-devel gcc make git autoconf autogen automake pkgconfig
```

2.下载和安装Netdata
```
[root@localhost ~]# cd /usr/local/src/
[root@localhost src]# wget http://firehol.org/download/netdata/releases/v1.0.0/netdata-1.0.0.tar.gz
[root@localhost src]# tar -xf netdata-1.0.0.tar.gz
[root@localhost src]# cd netdata-1.0.0
[root@localhost netdata-1.0.0]# ./netdata-installer.sh

wget https://github.com/netdata/netdata/releases/download/v1.24.0/netdata-v1.24.0.tar.gz
cd netdata-v1.24.0
./netdata-installer.sh

```


Netdata启动和关闭（CentOS）：

1.Netdata启动：
```
[root@localhost ~]#  /usr/sbin/netdata
```
2.Netdata关闭：
```
[root@localhost ~]#  killall netdata
```
 

Netdata配置（CentOS）：

配置文件在/etc/netdata/netdata.conf中。
```
[root@localhost ~]#  vim /etc/netdata/netdata.conf
```


docker run -d --name=netdata \
  -p 3008:19999 \
  -v netdatalib:/var/lib/netdata \
  -v netdatacache:/var/cache/netdata \
  -v /etc/passwd:/host/etc/passwd:ro \
  -v /etc/group:/host/etc/group:ro \
  -v /proc:/host/proc:ro \
  -v /sys:/host/sys:ro \
  -v /etc/os-release:/host/etc/os-release:ro \
  --restart unless-stopped \
  --cap-add SYS_PTRACE \
  --security-opt apparmor=unconfined \
  netdata/netdata
