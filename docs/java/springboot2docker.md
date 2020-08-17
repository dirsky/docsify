
```
cat Dockerfile

# VERSION 0.0.1
# AUTHOR guozhong.xu

FROM java:8

MAINTAINER guozhong.xu@qq.com


# VOLUME 指定了临时文件目录为/tmp。
# 其效果是在主机 /var/lib/docker 目录下创建了一个临时文件，并链接到容器的/tm
VOLUME /tmp

# 将jar包添加到容器中并更名为app.jar
COPY test-0331-boot-0.0.1-SNAPSHOT.jar app.jar

RUN bash -c "touch /app.jar"

EXPOSE 8080

ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]


cat docker-compose.yml

version: '3'
services:
  test-boot:
    build:
      context: ./
      dockerfile: ./Dockerfile
    restart: always
    container_name: gzxu-tb
    image: gzxu/test-boot
    ports:
      - 8080:8080

```
