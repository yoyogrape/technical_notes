# docker




### 第一节 核心概念
```
docker主机(Host)：安装了Docker程序的机器（Docker直接安装在操作系统之上）；
docker客户端(Client)：连接docker主机进行操作；
docker仓库(Registry)：用来保存各种打包好的软件镜像；
docker镜像(Images)：软件打包好的镜像；放在docker仓库中；
docker容器(Container)：镜像启动后的实例称为一个容器；容器是独立运行的一个或一组应用
```
### 第二节 安装docker
1）安装虚拟机  
2）配置好虚拟机的网卡  
3）查看虚拟机的ip，命令 
```
ip addr
``` 
4）查看虚拟机内核版本，必须是3.0以上版本。
```
uname -r
```
5）安装docker命令
```
yum install docker
```
6）输入y确认
7）启动docker
```
systemctl start docker

docker -v
```
8）开机启动docker
```
systemctl enable docker
```
9）停止docker
```
systemctl stop docker
```

### 第三节 docker 常用命令
docker仓库地址，可以查看库里面有没有想要的镜像。
```
https://hub.docker.com/
```
1）查询仓库中是否有该镜像
```
docker search mysql
docker search redis

查询说明：
OFFICIAL：是否是官方的
AUTOMATED：是否是自动构建的
```

2）下载让软件 标签可以指定版本
```
命令：
docker pull 镜像名 [标签]

[root@localhost docker]# docker pull mysql
Using default tag: latest //下载最新的mysql
```
3）查看所有本地镜像
```
docker images ///查看所有本地镜像
```
4）删除指定的本地镜像
```
docker rmi 镜像ID
```

### 第四节 解决docker启动问题
1）启动遇见报错信息
```
Job for docker.service failed because the control process exited with error code. 
See "systemctl status docker.service" and "journalctl -xe" for details.
```
2）查看docker服务状态信息
```
命令：
systemctl status docker.service

错误信息中有这样一句话：
Error starting daemon: SELinux is not supported wi...se)
```

3）网上查找原因：：：  
docker配置文件没有关闭selinux
```
vi /etc/sysconfig/docker
将--selinux-enabled参数改为false
OPTIONS='--selinux-enabled=false --log-driver=journald --signature-verification=false'
```
4）重启docker，问题解决
```
systemctl start docker
```

### 第五节 解决docker因为网络原因不能下载问题
1）下载bind-utils工具包  
bind是linux系统下的一个DNS服务程序.bind-utils是bind软件提供的一组DNS工具包,里面有一些DNS相关的工具.
主要有:dig,host,nslookup,nsupdate.使用这些工具可以进行域名解析和DNS调试工作.
```
yum install bind-utils
```
2）查找 registry-1.docker.io 找到可用IP
```
命令：
dig @114.114.114.114 registry-1.docker.io

[root@archlinux ~]# dig @114.114.114.114 registry-1.docker.io 
; <<>> DiG 9.12.0 <<>> @114.114.114.114 registry-1.docker.io 
; (1 server found) 
;; global options: +cmd 
;; Got answer: 
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 37534 
;; flags: qr rd ra; QUERY: 1, ANSWER: 8, AUTHORITY: 0, ADDITIONAL: 1 
;; OPT PSEUDOSECTION: 
; EDNS: version: 0, flags:; udp: 4096 
; COOKIE: 88f87a15f4ca45dc (echoed) 
;; QUESTION SECTION: 
;registry-1.docker.io. IN A 
;; ANSWER SECTION: 
registry-1.docker.io. 30 IN A 34.200.28.105 
registry-1.docker.io. 30 IN A 52.22.181.254 
registry-1.docker.io. 30 IN A 35.169.231.249 
registry-1.docker.io. 30 IN A 52.5.185.86 
registry-1.docker.io. 30 IN A 54.164.230.151 
registry-1.docker.io. 30 IN A 34.200.90.16 
registry-1.docker.io. 30 IN A 52.20.146.203 
registry-1.docker.io. 30 IN A 52.206.156.207 
;; Query time: 116 msec 
;; SERVER: 114.114.114.114#53(114.114.114.114) 
;; WHEN: 四 3月 08 09:27:12 CST 2018 
;; MSG SIZE rcvd: 189
```
3）尝试修改/etc/hosts强制docker.io相关的域名解析到其它可用IP
```
vi /etc/hosts

52.5.185.86 registry-1.docker.io
```
4）保存之后重试
```
systemctl start docker
```

### 第六节 运行镜像步骤
```
1、搜索镜像
[root@localhost ~]# docker search tomcat
2、拉取镜像
[root@localhost ~]# docker pull tomcat
3、根据镜像启动容器
docker run ‐‐name mytomcat ‐d tomcat:latest
    ‐‐name：给容器取的名字 
    ‐d：后台运行
4、docker ps
查看运行中的容器
5、 停止运行中的容器
docker stop 容器的id
6、查看所有的容器
docker ps ‐a
7、启动容器
docker start 容器id
8、删除一个容器
docker rm 容器id

9、启动一个做了端口映射的tomcat
[root@localhost ~]# docker run -d --name=DM_tomcat -p 8888:8080 tomcat:latest
‐d：后台运行
 --name=DM_tomcat：指定容器名称
‐p: 将主机的端口映射到容器的一个端口 主机端口:容器内部的端口
tomcat:镜像的名称    

10、为了演示简单关闭了linux的防火墙
service firewalld status ；查看防火墙状态
service firewalld stop：关闭防火墙
11、查看容器的日志
docker logs container‐name/container‐id
更多命令参看
https://docs.docker.com/engine/reference/commandline/docker/
可以参考每一个镜像的文档
12、可以用一个镜像启动多个镜像
```
### 第七节 mysql的安装
1）下载mysql
```
docker pull mys
```
2）先讲一种错误的启动方式，因为需要设置密码
```
docker run ‐‐name=mysql01 ‐d mysql

因为需要对数据库进行密码的设置，所以需要设置参数启动
[root@localhost ~]# docker run ‐p 3306:3306 ‐‐name mysql02 ‐e MYSQL_ROOT_PASSWORD=123456 ‐d mysql

```
3）正确的启动方式
```
[root@localhost ~]# docker run ‐p 3306:3306 ‐‐name mysql02 ‐e MYSQL_ROOT_PASSWORD=123456 ‐d
mysql
```
4）几个其他的高级操作
```
docker run ‐‐name mysql03 ‐v /conf/mysql:/etc/mysql/conf.d ‐e MYSQL_ROOT_PASSWORD=my‐secret‐pw
‐d mysql:tag
把主机的/conf/mysql文件夹挂载到 mysqldocker容器的/etc/mysql/conf.d文件夹里面
改mysql的配置文件就只需要把mysql配置文件放在自定义的文件夹下（/conf/mysql）
docker run ‐‐name some‐mysql ‐e MYSQL_ROOT_PASSWORD=my‐secret‐pw ‐d mysql:tag ‐‐character‐set‐
server=utf8mb4 ‐‐collation‐server=utf8mb4_unicode_ci
指定mysql的一些配置参数

```
### 第八节 docker中国
docker在下载镜像的时候连接的是国外的服务器，下载很慢，可以连docker中国，进行镜像加速。  
docker中国地址：`https://www.docker-cn.com/`  
下载命令：

```
docker pull registry.docker-cn.com/library/镜像名称
```


### 第九节 启动容器的时候报错如下

```
Error response from daemon: oci runtime error: container_linux.go:247: starting container process ca
```

解决方法:升级系统内核
```
sudo yum update
```