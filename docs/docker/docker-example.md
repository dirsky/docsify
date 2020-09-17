# docker-example

## 1. 数据库部分

### 1.1 docker pull mysql:5.7

```shell
docker run -d -p 3306:3306  -v /data/docker/mysql/datadir:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=Gz,.9918xu --name mysql mysql:5.7
# 不需要提前创建/data/docker/mysql目录
```

```shell
# 设置支持utf-8解决方案（已测试有效）
sudo docker exec -it mysql bash
echo "character-set-server=utf8" >> /etc/mysql/mysql.conf.d/mysqld.cnf
exit
sudo docker restart mysql
```

```shell
docker exec -it mysql bash
mysql -uroot -p
# 给root设置密码
ALTER  USER  'root'  IDENTIFIED  WITH  mysql_native_password  BY  '123456';
# 添加远程登录用户
CREATE USER 'admin'@'%' IDENTIFIED WITH mysql_native_password BY '123456';
GRANT ALL PRIVILEGES ON *.* TO 'admin'@'%';
```

```shell
docker ps -a --no-trunc # ps列出container -a列出全部, --no-trunc不截断
docker stop mysql
docker restart mysql
docker rm mysql
```

### 1.2 docker pull redis

```
docker run -d -p 6379:6379 --name redis redis --requirepass "Gzxu2012"
# 上面的第二个redis是repository或者image id
# 查看redis版本
docker exec -it redis redis-server -v
```

### 1.3 docker pull mongo

```
docker run -d -p 27017:27017 -v /data/docker/mongo/db:/data/db --name mongo mongo --auth
注意：前面27017是虚拟机的端口，后面27017是docker内的端口
注意：重新创建的时候记得删除/data/docker/mongo/db下的数据
```

```
# 用户及权限管理
docker exec -it mongo mongo admin
```
1.数据库用户角色：read、readWrite;
2.数据库管理角色：dbAdmin、dbOwner、userAdmin；
3.集群管理角色：clusterAdmin、clusterManager、clusterMonitor、hostManager；
4.备份恢复角色：backup、restore
5.所有数据库角色：readAnyDatabase、readWriteAnyDatabase、userAdminAnyDatabase、dbAdminAnyDatabase
6.超级用户角色：root

```
db.createUser(
    {
        user:"root",
        pwd:"Gzxu2012",
        roles:["root"]
    }
);
db.auth("root","Gzxu2012");
show dbs;
show tables;
show users;
db.system.users.find();

# 删除用户
db.dropUser('frank')

注意此时添加的用户都只用于admin数据库，而非你存储业务数据的数据库
db.createUser({ user: 'admin', pwd: 'Gzxu2012', roles: [{ role: "userAdminAnyDatabase", db: "admin" } ] }); 
# 必须先通过此处登录一下，然后切换其他db才能创建用户。
# 用户创建在哪个db下就说明登录的时候需要验证哪个db
use admin;
db.auth("admin","Gzxu2012");

db.createUser({ user: 'rw', pwd: 'Gzxu2012', roles: ['readWriteAnyDatabase'] });
# 此用户需要验证admin数据库，但可以读写所有数据库。
db.createUser({ user: 'frank', pwd: 'Gzxu2012', roles: [ { role: "readWrite", db: "app" } ] });
# 此用户需要验证admin数据库，但可以读写app数据库。

use app;
db.createUser({ user: 'app', pwd: 'Gzxu2012', roles: [ { role: "readWrite", db: "app" } ] });
# 此用户需要验证app数据库，但可以读写app数据库。

# 退出重新登录，写入测试数据
use app;
db.auth("app","Gzxu2012");
db.test.save({name:"gzxu@vip.qq.com"});

# 需要切换到use对应的db上
db.runCommand(
    {
        updateUser:"frank",
        roles:[{role:"readWrite", db:"hi"},{role:"readWrite", db:"app"}]
    }
);

db.test.save({name:"frank"});
db.test.insert()  --直接插入不更新
# 只更新找到的第一条，并且会替换该字段
db.test.update({name:"frank"},{age:16});
# 只更新找到的第1条数据，新增字段
db.test.update({name:"frank"},{$set:{age:123}});
# 更新所有找到匹配的数据
db.test.update({name:"frank"},{$set:{age:30}}, {multi: true});
db.test.find();
```

### 1.3 docker pull postgres:9.6

```
docker run -d -p 5432:5432 -v /data/docker/postgres/pgdata:/var/lib/postgresql/data -e POSTGRES_PASSWORD=Gz,.9918xu --name pg postgres:9.6
# 注意：postgres镜像默认的用户名为postgres， 登陆口令为创建容器是指定的值。
# 执行下列sql语句看版本
select version();
```

### 1.4 docker pull elasticsearch

```
docker run -d -p 9200:9200 -p 9300:9300 -e ES_JAVA_OPTS="-Xms256m -Xmx256m" --name es elasticsearch

# 访问
http://docker:9200

sysctl -w vm.max_map_count=262144

docker pull kibana
docker run --name kibana -p 5601:5601 \
--link es:es \
-e "elasticsearch.host=http://es:9200" \
-d kibana
```

### 1.5 docker pull couchbase

```
docker run -d --name cb -p 8091-8094:8091-8094 -p 11210:11210 couchbase
http://docker:8091
docker exec -it cb bash
cd /opt/couchbase/bin
./cbq -u admin -p Gzxu2012 -engine=http://127.0.0.1:8091/
cbq> SELECT callsign FROM `travel-sample` LIMIT 5;

# 镜像的导入导出
# 容器的导入导出
docker export f2582758af13 | gzip > ubuntu-web.tar.gz
zcat ubuntu-web.gz | docker import - ubuntu-web
```


## 2. 容器

### 2.1 docker pull tomcat

```
docker run -d -p 8899:8080 -v /data/docker/tomcat/webapps:/usr/local/tomcat/webapps --name tc tomcat:8.5

docker exec -it tc bash
cp webapps.dist/* webapps/
不需要重启
实际上只需要在本地的webapps下创建ROOT目录即可，不需要重命名或者拷贝文件到webapps下
```

```
可以利用manager进行容器内的管理，
容器内修改：conf/tomcat-user.xml下，分别对mamager和host-manager进行管理
<role rolename="manager-gui"/>
  <role rolename="admin-gui"/>
  <user username="tomcat" password="nokia3" roles="manager-gui"/>
  <user username="admin" password="nokia3" roles="admin-gui"/>

容器外修改webapps/manager/META-INF/context.xml替换allow="^.*$"内容
<Context antiResourceLocking="false" privileged="true" >   <Valve className="org.apache.catalina.valves.RemoteAddrValve" allow="^.*$" /> </Context>
```
```
解决tomcat访问Oracle时区的问题
ln -snf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && echo Asia/Shanghai > /etc/timezone
```

### 2.2 docker pull nginx

```
docker run -d -p 9901:80 -v /data/docker/nginxs/nginx9901/www:/usr/share/nginx/html -v /data/docker/nginxs/nginx9901/conf/conf.d:/etc/nginx/conf.d -v /data/docker/nginxs/nginx9901/logs:/var/log/nginx --name ng9901 nginx

docker run -d -p 88:80 -v /data/docker/nginx/www:/usr/share/nginx/html -v /data/docker/nginx/conf/conf.d:/etc/nginx/conf.d -v /data/docker/nginx/logs:/var/log/nginx --name ng nginx
```
```
vim /data/docker/nginx/conf/conf.d/default.conf

server {
    listen       80;
    server_name  localhost;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }


    # 匹配url中带有api的，并转发
    location /api {
        # 利用正则进行匹配#去掉api前缀，$1是正则中的第一串,这样后端的接口也不需要带api了
        rewrite ^/api/(.*)$ /$1 break;
        # 转发的参数设定
        proxy_pass http://192.168.3.66:8899;
    }


    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}

```
```
vim /data/docker/nginx/www/index.html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Frank’s Title</title>
</head>
<body>
    <h1>我的第一个标题</h1>
    <p>我的第一个段落。</p>
</body>
</html>
```

### 2.3 docker pull httpd:2.4

```
docker run -dit --name apache2 -p 99:80 -v /data/docker/apache2/www/:/usr/local/apache2/htdocs/ httpd:2.4
docker exec -it apache2 bash
mkdir –p /data/docker/apache2/conf/
docker cp apache2:/usr/local/apache2/conf/httpd.conf /data/docker/apache2/conf/httpd.conf
ls /data/docker/apache2/conf
docker stop apache2;docker rm apache2

docker run -dit --name apache2 -p 99:80 -v /data/docker/apache2/www/:/usr/local/apache2/htdocs/ -v /data/docker/apache2/conf/httpd.conf:/usr/local/apache2/conf/httpd.conf httpd:2.4
```
```
sudo vim /data/docker/apache2/conf/httpd.conf
按G键（大写的），切到vi的最后一行，添加如下代码:IndexOptions Charset=UTF-8添加完成后输入:wq!，保存并退出vi。
docker restart apache2

1)找到 LoadModule 对应的位置，加入以下代码：
LoadModule autoindex_module modules/mod_autoindex.so 
Include conf/extra/httpd-autoindex.conf
2) 找到以下代码：
<Directory />
    AllowOverride none
    Require all deny
</Directory>
修改deny为 granted
找到<Directory "/usr/local/apache2/htdocs">
Options Indexes FollowSymLinks
    AllowOverride None
    # Allow open access:
    Require all granted
    allow from all
    IndexOptions FancyIndexing FoldersFirst NameWidth=* DescriptionWidth=* SuppressHTMLPreamble HTMLTable
    IndexOptions Charset=utf-8 IconHeight=16 IconWidth=16 SuppressRules
    IndexIgnore web header.html footer.html actions defects
    HeaderName /web/header.html
    ReadmeName /web/footer.html
    IndexOrderDefault Ascending Date
ServerSignature Off
3）apache2\www\web
增加header.html，footer.html
```

### 2.4 docker pull fauria/vsftpd

```
docker run -d --name ftp -p 21:21 -p 20:20 -p 21100-21110:21100-21110 -v /root/docker/ftp:/home/vsftpd -e FTP_USER=ftpuser -e FTP_PASS=ftpuser -e PASV_ADDRESS=192.168.72.111 -e PASV_MIN_PORT=21100 -e PASV_MAX_PORT=21110 fauria/vsftpd

#docker exec -i -t ftp bash
#vi /etc/vsftpd/virtual_users.txt
#mkdir /home/vsftpd/ftpuser
#/usr/bin/db_load -T -t hash -f /etc/vsftpd/virtual_users.txt /etc/vsftpd/virtual_users.db
#docker restart ftp

# 一定要确保端口通过安全组
ftp://ftpuser:ftpuser@192.168.72.111/
```


## 3. 其他/未分类

### 3.1 docker pull rabbitmq:3.7-management

```
docker run -d -p 5672:5672 -p 15672:15672 --name mq rabbitmq:3.7-management
http://cvm:15672
guest,guest
```

### 3.2 docker pull wordpress

```
docker run -d -p 8081:80 -e WORDPRESS_DB_HOST=192.168.72.111:3306 -e WORDPRESS_DB_USER=root -e WORDPRESS_DB_PASSWORD=password --name wp wordpress
```

### 3.3 docker pull jenkins

```
docker run -d -p 86:8080 -p 50000:50000 -v /etc/localtime:/etc/localtime --name jk jenkins
docker exec jk tail /var/jenkins_home/secrets/initialAdminPassword
```

### 3.4 docker pull redmin

```
# 先启动postgres
docker run -d -p 3000:3000 -v /root/docker/redmine/datadir:/usr/src/redmine/files --link pg:postgres --name red redmine
http://docker:3000
```

### 3.5 docker pull owncloud

```
docker run -d -p 89:80   -v /data/docker/owncloud:/var/www/html --link mysql:mysql --name oc owncloud
--link my-mysql:mysql ：将 owncloud容器(客户) 链接到mysql容器(服务)，链接别名(可省略)：mysql。此处的意思是在owncloud开始配置的时候，选择mysql/mariadb，输入数据库的用户名和密码以及新建数据库的名字如own_cloud，重要的是最下方localhost修改为别名mysql。
```

### 3.6 docker pull portainer/portainer

```
docker run -d -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock --restart=always --name ui portainer/portainer
```

### 3.7 docker pull gitlab/gitlab-ce

```
#比较大，需要的资源比较多，未成功
# gitlab-ce为稳定版本，后面不填写版本则默认pull最新latest版本
docker run -d -p 443:443 -p 8899:8899 -p 222:22 -v /root/docker/gitlab/config:/etc/gitlab -v /root/docker/gitlab/logs:/var/log/gitlab -v  /root/docker/gitlab/data:/var/opt/gitlab  --name gl gitlab/gitlab-ce

vim /root/docker/gitlab/config/gitlab.rb
# 配置http协议所使用的访问地址,不加端口号默认为80
external_url 'http://192.168.72.111'

# 配置ssh协议所使用的访问地址和端口
gitlab_rails['gitlab_ssh_host'] = '192.168.72.111'
gitlab_rails['gitlab_shell_ssh_port'] = 222 # 此端口run时22映射的222端口

docker restart gl
```

### 3.8 docker pull ubuntu:15.10

```
docker run --name ub ubuntu:15.10 /bin/echo "Hello world"
docker logs ub
docker run -i -t --name ubi ubuntu:15.10 /bin/bash
# -t:在新容器内指定一个伪终端或终端。
# -i:允许你对容器内的标准输入 (STDIN) 进行交互。
docker run -d --name ubd ubuntu:15.10 /bin/sh -c "while true; do echo hello world; sleep 1; done"
docker logs ubd
# 日志随容器的删除而删除

# 再次进入交互环境
docker exec -it ub bash 
docker exec -it ub sh
```

### 3.9 docker pull python:2.7

```
docker run -it --rm --name py2 -v "$PWD":/usr/src/myapp -w /usr/src/myapp python:2.7 python py.py
```

### 3.10 docker pull python:3.7.7

```
docker run -it --rm --name py3 -v "$PWD":/usr/src/myapp -w /usr/src/myapp python:3.7.7 python first.py
# 经过测试，这类docker都会运行一下直接退出，不保存container
```

### 3.11 docker pull netdata

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
```
