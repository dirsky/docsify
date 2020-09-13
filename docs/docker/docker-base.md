
> Author: gzxu@vip.qq.com
>
> Date: July 25 2020

## 1. Install and Config Docker

### 1.1 Before Install

```shell
uname -r # Docker require CentOS core above 3.10
# with ubuntu：sudo apt
# install dependies package:yum-util offer yum-config-manager，another two is dependies of devicemapper
yum install -y yum-utils device-mapper-persistent-data lvm2

# set official yum source
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo 
# set aliyun yum source
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo 

# view config is success
vim /etc/yum.repos.d/docker-ce.repo

# uninstall old version
yum remove docker  docker-common docker-selinux docker-engine
yum update # ensure yum package is up to date. a long time...
```


### 1.2 Install

```shell
# list all docker version, choose offical version
yum list docker-ce --showduplicates | sort -r 
# 由于repo中默认只开启stable仓库，故这里安装的是最新稳定版17.12.0
yum install docker-ce -y 
# verify install is success, client and service should up
docker version
```


### 1.3 start docker

```shell
systemctl start docker 
systemctl enable docker
systemctl status docker
```



### 1.4 config docker image source

```shell
# 配置镜像地址,注意地址是自己申请的，有可能失效，需要更新确认。
# 阿里云-控制台-https://cr.console.aliyun.com/cn-hangzhou/instances/repositories
# 其他镜像"registry-mirrors": ["http://hub-mirror.c.163.com"]

sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://8q8njuiz.mirror.aliyuncs.com"]
}
EOF

sudo systemctl daemon-reload
sudo systemctl restart docker

# 解释：tee 命令相当于管道的一个T型接头。它将从 STDIN 过来的数据同时发往两处。一处是STDOUT ，另一处是 tee 命令行所指定的文件名

{
   "registry-mirrors": [
       "https://mirror.ccs.tencentyun.com"
  ]
}
```

## 2 Edit and Inspect Docker

### 2.1 change port

```shell
# centos下容器的配置文件路径,其中的hashofthecontainer是docker镜像的hash值，可以通过docker ps或者docker inspect containername查看。（CONTAINER ID就可以看出来）

vim /var/lib/docker/containers/[hash_of_the_container]/hostconfig.json

# 文件中其中有一项是PortBindings，其中8080/tcp对应的是容器内部的8080端口，HostPort对应的是映射到宿主机的端口9190。

systemctl restart docker
```

### 2.2 stats docker Mem and CPU

```shell
# 查看所有docker容器占用内存、CPU情况
docker stats $(docker ps --format={{.Names}})
```

### 2.3 Docker logs

```shell
docker logs --help
docker logs -f -t --since="2017-05-31" --tail=10 container_name

# --since : 此参数指定了输出日志开始日期，或者 --since 34m。
# -f : 查看实时日志
# -t : 查看日志产生的日期
# -tail=10 : 查看最后的10条日志。
# container_name : 容器名称
```

### 2.4 into Docker container

```shell
docker exec -it ub bash
# ub is container name
# exit to exit container
# docker default do not support vim
```

### 2.5 docker install vim

```shell
apt-get update
apt-get install vim
```

### 2.6 centos下新用户使用docker

```shell
解决方案：
sudo gpasswd -a $USER docker #将当前用户添加至docker用户组
newgrp docker #更新docker用户组

# 错误：Got permission denied while trying to connect to the Docker daemon socket
# 自己理解：docker默认对docker用户组给与权限
# 本质是修改了/etc/group 下的docker:x:992:pc,mac,node
sudo groupadd docker  # 添加docker用户组
sudo gpasswd -a $frank docker  # 检测当前用户frank是否已经在docker用户组中
```

### 2.7 docker与本机传文件

```shell
# docker container to host
# docker cp tc:docker容器内的文件全路径 本机保存文件的全路径
docker cp tc:/data/configure.txt E:\PHP\configure.txt

# host to container
# docker cp 本机保存文件的全路径 container_id:docker容器内的文件全路径
docker cp E:\PHP\configure.txt tc:/data1/configure.txt
```
