============== 下面是Dockerfile ==============

```
# VERSION 0.0.1

FROM java:8

MAINTAINER guozhong.xu@qq.com

# VOLUME 指定了临时文件目录为/tmp。
# 其效果是在主机 /var/lib/docker 目录下创建了一个临时文件，并链接到容器的/tm
VOLUME /tmp

# 将jar包添加到容器中并更名为app.jar
COPY test-0331-boot-0.0.1-SNAPSHOT.jar app.jar

RUN bash -c "touch /app.jar"

EXPOSE 8080

# 为了缩短 Tomcat 的启动时间，添加java.security.egd的系统属性指向/dev/urandom
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]




docker build -t gzxu/test-boot .
docker run -d -p 8080:8080 --name tb gzxu/test-boot
docker rmi -f id

# 保存包含依赖的镜像
docker save > test-boot.tar gzxu/test-boot 
# -o：指定保存的镜像的名字；test-boot.tar：保存到本地的镜像名称；gzxu/test-boot。

# 载入镜像
docker load < test-boot.tar

yum -y install openssl openssl-devel ncurses-devel.x86_64  bzip2-devel sqlite-devel python-devel zlib
yum install -y epel-release && yum install -y python-pip && pip install --upgrade pip && pip install docker-compose

docker-compose  version

docker-compose up -d

```
