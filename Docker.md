# Docker

## 1.Docker概述

> java----jar(环境) -----打包项目带上环境(镜像) ------(Docker仓库===应用商店) -----运维下载我们发布的镜像----直接运行即可

Docker的思想就来自于集装箱！

隔离：Docker核心思想，项目打包装箱，每个箱子是互相隔离的

Docker基于go语言开发的，开源项目！

文档地址：<a>https://docs.docker.com/</a>

仓库地址：<a>https://hub.docker.com/</a>

### 1.1 Docker与虚拟机

虚拟机技术缺点：

1.资源占用十分多

2.冗余步骤多

3.启动很缓慢

> 容器化技术

==容器化技术不是模拟的一个完整的操作系统==

比较Docker和虚拟机技术的不同：

- 传统虚拟机，虚拟出一个完整的操作系统，然后在这个系统上安装和运行软件
- 容器内的应用直接运行在宿主机的内容，容器是没有自己的内核的，也没有虚拟我们的硬件，所以就轻便了
- 每个容器间是互相隔离的，每个容器内都有一个属于自己的文件系统，互不影响

> DevOps（开发/运维）

==应用更快速的交付和部署==

Docker：打包镜像发布测试，一键运行----更便捷的升级和扩容；部署应用就和搭积木一样！

==Docker是内核级别的虚拟化，可以在一个物理机上运行很多的容器实例！服务器的性能可以被压榨到极致==

## 2.Docker安装

### Docker的基本组成

![](../%E6%88%AA%E5%9B%BE/Docker%E7%9A%84%E6%9E%B6%E6%9E%84.jpg)

**镜像(image)：**

Docker镜像就好比是一个模板，可以通过这个模板来创建容器服务；比如tomcat镜像====>run====>tomcat01容器（提供服务器），通过这个镜像可以创建多个容器（最终服务运行或者项目运行就是在容器中）

**容器(container):**

Docker利用容器技术，独立运行一个或者一组应用，通过镜像来创建的；启动，停止，删除，创建需要一系列的命令

仓库(repository):

仓库就是存放镜像的地方；仓库分为公有仓库和私有仓库

### 2.1 安装Docker

```shell
#1.卸载旧的版本
yum remove docker \
									docker-client \
									docker-client-latest\
									docker-common\
									docker-latest\
									docker-latest-logrotate\
									docker-logrotate\
									docker-engine
#2.需要的安装包
yum install -y yun-utils

#3.设置镜像的仓库
yum-config-manager\
			--add-repo\
			https://download.docker.com/linux/centos/docker-ce.repo #默认是国外镜像（速度较慢）
yum-config-manager\
			--add-repo\
			https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
#4.安装docker相关的 docker-ce 社区版docker
yum install docker-ce docker-ce-cli containerd.io

#5.启动docker
systemctl start docker
#6.检测是否安装成功
docker version
#7.运行hello-world
docker run hello_world
#8.查看下载的镜像
docker images
```

### 2.2 阿里云镜像加速

登录阿里云，找到容器服务，对应的镜像加速器；找到镜像加速地址，配饰使用

### 2.3 Docker的运行流程

![image-20201201234526050](../%E6%88%AA%E5%9B%BE/Docker%E6%B5%81%E7%A8%8B%E5%9B%BE.png)

**底层原理**

Docker是怎么工作的？

Docker是一个Client-Server结构的系统，Server的守护进程运行在主机上。通过Socket从客户端访问

DockerServer接收到Docker-Client的指令，就会执行这个指令

Docker为什么比VM快？

1.Docker有着比虚拟机更少的抽象层

2.Docker利用的是宿主机的内核，VM需要使用GuestOs

![image-20201201235316745](../%E6%88%AA%E5%9B%BE/Docker%E6%AF%94VM%E5%BF%AB.png)

## 3.Docker的常用命令

**帮助命令**

```shell
docker version
docker info
docker 命令 --help #万能命令
```

#### **镜像命令**

**docker images** 查看所有本地的主机上的镜像

```SHELL
[root@lory /]# docker images
REPOSITORY   TAG  		IMAGE ID 			CREATED		 SIZE
hello-world  latest bf756fblae65  4months ago   13.3KB

#解释
REPOSITORY  镜像的仓库源
TAG         镜像的标签
IMAGE ID    镜像的ID
CREATED     镜像的创建时间
SIZE        镜像的大小

#可选项
-a,--all              #列出所有的镜像
		--digests 					
-f,--filter filter		#过滤
		 --format string  #格式化
-q,--quiet            #只显示镜像的ID
```

**docker search** 镜像搜索

```shell
[root@lory /]# docker search mysql
NAME                      DESCRIPTION                                    STARS
OFFICIAL			AUTOMATED  
mysql											MySQL is a widely used,open-source relation     9494
[OK]
mariadb										MariaDB is a community-developed fork of MyS... 3441
[OK]

#可选项
--filter=STARS=3000 #搜索出来的镜像就是STARS大于3000的
```

**docker pull** 下载镜像

```shell
#下载镜像 docker pull 镜像名 [:tag]
[root@lory /]# docker pull mysql
```

**docker rmi** 删除镜像

```shell
[root@lory /]# docker rmi -f 镜像ID #删除指定的镜像
[root@lory /]# docker rmi -f 镜像ID 镜像ID 镜像ID 镜像ID #删除多个镜像
[root@lory /]# docker rmi -f $(docker images -aq) #删除所有的镜像
```

#### 容器命令

说明：==有了镜像才可以创建容器==

**新建容器并启动**

```shell
docker run [可选参数] image

#参数说明
--name="Name" 容器名字，用来区分容器
-d            后台方式运行
-i/-t					使用交互方式运行，进入容器查看内容
-p            指定容器的端口；eg：-p 8080：8080
		-p ip：主机端口：容器端口
		-p 主机端口：容器端口（常用）
		-p 容器端口
		容器端口
		
#启动进入容器
[root@lory /]# docker run -it centos /bin/bash
[root@e4eccc01b495 /]#

#退出容器
exit #容器停止并退出
crtl+P+Q #容器不停止退出
```

**列出所有的运行的容器**

```shell
#docker ps 命令
-a #列出当前正在运行的容器+带出历史运行过的容器
-n=？ #显示最近创建的容器
-q #只显示容器的编号
```

**删除容器**

```shell
docker rm 容器ID   #删除指定的容器
docker rm -f $(docker ps -aq) #删除所有的容器
```

**启动和停止容器的操作**

```shell
docker start 容器ID #启动一个容器
docker restart 容器ID #重启一个容器
docker stop 容器ID #停止一个容器
docker kill 容器ID #杀死一个容器
```

#### 常用的其他命令

**后台启动容器**

```shell
#命令 docker run -d 镜像名
[root@lory /]#docker run -d centos

#问题docker ps 发现centos停止
#原因：docker容器使用后天运行，就必须要有一个前台进程，docker发现没有应用，就会自动停止
```

**查看日志**

```shell
docker logs -tf --tail 要显示的日志条数 容器ID
```

**查看容器中进程信息**

```shell
#命令 docker top 容器Id
[root@lory /]# docker top dce7b86171bf
UID            PID          PPID        C           STIME
root           22891         22875      0            21:21
root           23351        22891     0            21:25
```

**查看镜像的元数据**

```shell
#命令 docker inspect 容器Id
[root@lory]# docker inspect dce7b86171bf
```

**进入当前正在运行的容器**

```shell
#通常容器都是使用后台方式运行的，需要进入容器中修改一些配置
#命令
docker exex -it 容器ID /bin/bash

#方式二
docker attach 容器ID

#区别：
exec 进入容器后开启一个新的终端，可以在里面操作；
attach进入容器正在执行的终端，不会启动新的终端进程
```

**从容器内拷贝文件到主机上**

```shell
#命令
docker cp 容器ID：容器内路径 目标主机路径
```

## 3.Docker镜像

### 镜像是什么

镜像是一种轻量级、可执行的独立软件包，用来打包软件运行环境和基于运行环境开发的软件，它包含运行某个软件所需的所有内容，包括代码、运行时需要的库、环境变量和配置文件。

所有的应用，直接打包Docker镜像就可以直接跑起来！

### Docker镜像加载原理

> UnionFs（联合文件系统）

UnionFs（联合文件系统）：Union文件系统是一种分层、轻量级并且高性能的文件系统。它支持对文件系统的修改作为一次提交来一层层的叠加，同时可以将不同目录挂载到同一个虚拟文件系统下。Union文件系统是Docker镜像的基础。镜像可以通过分层来进行继承，基于基础镜像（没有父镜像），可以制作各种具体的应用镜像

特性：一次同时加载多个文件系统，但从外面看起来，只能看到一个文件系统，联合加载会把各层文件系统叠加起来，这样最终的文件系统会包含所有底层的文件和目录

> Docker镜像加载原理

Docker的镜像实际上由一层一层的文件系统组成，这种层级的文件系统UnionFs。

bootFs（boot file system）主要包含bootloads和kernel，bootloader主要是==引导加载==kernel，linux刚启动时会加载bootfs文件系统，在Docker镜像的最底层是bootfs。这一层与我们典型的Linux/Unix系统是一样的，包含boot加载器和内核。当boot加载完成之后整个内核就都在内存中了，此时内存的使用权已由bootfs转交给内核，此时系统也会卸载bootfs。

rootfs（root file system）在bootfs上，包含的就是典型Linux系统中的/dev，/proc，/bin，/etc等标准目录和文件。rootfs就是各种不同的操作系统发行版，比如Ubuntu、Centos等等

![image-20201206192550360](../%E6%88%AA%E5%9B%BE/Docker%E9%95%9C%E5%83%8F%E5%8E%9F%E7%90%86.png)

对于精简的OS，rootfs可以很小，只需要包含最基本的命令、工具和程序库就可以了，因为底层直接用host和kernel，自己只需要提供rootfs就可以了。由此可见对于不同的linux发行版，bootf基本是一致的，rootfs会有差别，因此不同的发行版可以公用bootfs

### 分层理解

> 特点

Docker镜像都是只读的，当容器启动时，一个新的可写层被加载到镜像的顶部！这一层就是我们通常说的容器层，容器之下的都叫镜像层

### 如何提交一个自己的镜像

**commit镜像**

> docker commit 提交容器成为一个新的副本
>
> docker commit -m=“提交的描述信息” -a=“作者” 容器ID 目标镜像名：[tag]

## 容器数据卷

### 什么是容器数据卷

实现数据持久化，容器之间可以有一个数据共享的技术！Docker容器中产生的数据，同步到本地。实质就是目录的挂载，将我们容器内的目录，挂载到Linux上面！==做一个数据的同步==

![image-20201206195421172](../%E6%88%AA%E5%9B%BE/%E5%AE%B9%E5%99%A8%E6%95%B0%E6%8D%AE%E5%8D%B7%E5%8E%9F%E7%90%86.png)

### 使用数据卷

> 方式一：直接使用命令来挂载 -v

```shell
docker run -it -v 主机目录：容器内目录
```

```shell
#案例
[root@lory home]#docker run -it -v /home/ceshi:/home centos /bin/bash
```

启动之后，我们可以通过docker inspect 容器ID 命令查看容器的详细信息

![image-20201206200322984](../%E6%88%AA%E5%9B%BE/%E6%8C%82%E8%BD%BD%E6%95%B0%E6%8D%AE%E7%9B%AE%E5%BD%95.png) 

**例子：**Mysql的数据持久化问题

```shell
#获取镜像
[root@lory home] #docker pull mysql:5.7

#运行容器，需要做数据挂载！#安装mysql，需要配置启动密码。
#官方教程：docker run --name some-mysql -e MYSQL_ROOT_PASSWORD-my-secret-pw -d mysql:tag

#启动我们的mysql
-d  后台运行
-p  端口映射
-v  数据卷挂载
--name 容器名字
[root@lory home]#docker run -d -p 3310:3306 -v /home/mysql/conf:/etc/mysql/conf.d -v
/home/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name mysql01 mysql 5.7

#启动成功之后，可以使用外部的可视化数据库工具测试数据是否挂载成功
```

## 具名和匿名挂载

```shell
#匿名挂载
-v 容器内路径
docker run -d -p --name nginx01 -v /etc/nginx nginx

#查看所有卷的情况
[root@lory home]#docker volume ls

#具名挂载
[root@lory home]#docker run -d -p --name nginx02 -v juming_nginx:/etc/nginx nginx

#通过 -v 卷名：容器内路径
#查看该卷的具体位置
[root@lory home] #docker volume inspect juming_nginx
[
  {
		"CreatedAt":"2020-12-07T19:37:31+08:00",
		"Driver":"local",
		"Labels":null,
		"Mountpoint":"/var/lib/docker/volumes/juming_nginx/_data",
		"Name":"juming_nginx",
		"Options":null,
		"Scope":"local"
  }
]
```

> 所有的docker容器内的卷，没有指定目录的情况下都是在‘/var/lib/docker/volumns/xxx/——data’

```shell
#如何确定是具名挂载还是匿名挂载，还是指定路径挂载
-v 容器内路径   #匿名挂载
-v 卷名：容器内路径 #具名挂载
-v /宿主机路径：容器内路径 #指定路径挂载
```

#### 拓展：

```shell
#通过 -v 容器内路径：ro 或rw 改变容器读写权限
ro   readonly  #只读
rw   readwrite  #可读可写

#一旦设置了这个容器权限，容器对我们挂载出来的内容就有限定了
docker run -d -p --name nginx02 -v juming-nginx:/etc/nginx:ro nginx

#ro 只要看到ro就说明这个路径只能通过宿主机来操作，容器内是无法操作的
```

## Dockerfile

Dcokerfile就是用来构建docker镜像的构建文件===命令脚本；通过这个脚本就可以生成镜像，镜像是一层一层的，脚本是一个个的命令，每个命令都是一层

```shell
#dockerfile脚本
FROM centos
VOLUME ["volume01","volume02"]

CMD echo "---end---"
CMD /bin/bash
```

> 通过dockersfile脚本构建镜像

```shell
[root@lory docker-test-volume]#docker build -f /home/docker-test-volume/dockerfile11 -t lory/centos 1.0
```

### 数据卷容器

> 多个容器之间数据共享

```shell
#启动docker01
[root@lory /]#docker run -it --name docker01 lory/centos:1.0

#启动docker02 并继承docker01
[root@lory /]#docker run -ir --name docker02 --volumes-from docker01 lory/centos:1.0 
```

![image-20201207213132375](../%E6%88%AA%E5%9B%BE/%E5%AE%B9%E5%99%A8%E6%95%B0%E6%8D%AE%E5%8D%B7.png)

结论：

容器之间配置信息的传递，数据卷容器的生命周期一直持续到没有容器使用为止。

一旦持久化到了本地，这个时候，本地的数据是不会删除的

## DockerFile详解

dockerfile是用来构建docker镜像的文件！命令参数脚本！

构建步骤:

1、编写一个dockerfile 文件

2、docker build 构建成为一个镜像

3、docker run 运行镜像

4、docker push 发布镜像到DockerHub或者阿里云镜像仓库

### DockerFile镜像的构建过程

基础知识：

1、每个保留关键字（指令）都必须是大写字母

2、从上到下顺序执行

3、#表示注释

4、每一个指令都会创建提交一个新的镜像层，并提交 

DockerFile：构建文件，定义了一切的步骤。

DockerImages：通过DockerFile构建生成的镜像，最终发布和运行的产品！

Docker容器：容器就是镜像运行起来提供服务

### DockerFile的指令

```shell
FROM         #基础镜像
MAINTAINER   #镜像作者是谁  ---姓名+邮箱
RUN  				 #镜像构建时需要运行的命令
ADD					 #比如tomcat镜像，添加的toncat压缩包就是添加内容
WORKDIR			 #镜像的工作目录
VOLUME			 #挂载的目录
EXPOSE       #指定暴露端口
RUN          #运行
CMD          #指定这个容器启动的时候需要运行的命令，只有最后一个会生效，且可被替代
ENTRYPOINT   #指定这个容器启动的时候需要运行的命令，可以追加命令
ONBUILD      #当构建一个被继承DockerFile，这个时候就会运行ONBUILD的指令。触发指令
COPY         #类似ADD，将我们的文件拷贝到镜像中
ENV          #构建的时候设置环境变量
```

#### 案例：

DockerHub中大部分镜像都是从这个基础镜像开始的FROM scratch

```shell
#1、编写dockerFile文件
FROM centos
MAINTAINER lory<1301494645@qq.com>

ENV MYPATH /usr/local
WORKDIR $MYPATH

RUN yum -y install vim
RUN yum -y install net-tools

EXPOSE 80

CMD echo $MYPATH
CMD echo "-----end-----"
CMD /bin/bash

#2、通过这个文件构建镜像
#命令：docker build -f dockerfile文件路径 -t 镜像名：[tag]
```

### 案例：构建Tomcat镜像

1、准备tomcat和jdk压缩包

2、编写dockerFile文件,官方命名‘Dockerfile’，build会自动寻找这个文件，就不需要-f指定文件路径了

```shell
FROM centos
MAINIAINER lory<1301494645@qq.com>

COPY readme.txt /usr/locla/readme.txt

ADD jdk-8ull-linux-x64.tar.gz /usr/local/
ADD apache-tomcat-9;0.22.tar.gz /usr/local

RUN yum -y install vim

ENV MYPATH /usr/local
WORKDIR $MYPATH

ENV JAVA_HOME /usr/local/jdk1.8.0_11
ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.22
ENV CATALINA_BASH /usr/local/apache-tomcat-9.0.22
ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin

EXPOSE 8080

CMD /usr/local/apache-tomcat-9.0.22/bin/startup.sh && tail -F /usr/local/apache-tomcat-9.0.22/bin/logs/catalina.out
```

3、构建镜像

```shell
#docker build -t myTomcat .
```

4、启动镜像

5、发布项目（由于做了卷挂载，所以可以直接在本地编写项目就可以发布了）

## 发布自己的镜像

第一种：发布到DockerHub上

```shell
[root@lory tomcat] #docker login -help

Options:
	-p,--password String Password
		 --password-stdin  Take the password from stdin
	-u,--username String Username
```

 ```shell
#登录
docker login -u lory

#push 推送镜像到dockerhub上
docker push 镜像名：版本号

#push时可以遇到问题：an image does not exist locally with the tag

#解决方法：增加一个tag
docker tag 镜像名 新的镜像名（带版本号）
 ```

第二种：发布到阿里云镜像

1、登录阿里云

2、找到容器镜像服务

3、创建命名空间

4、创建容器镜像

5、提交本地镜像

```shell
[root@lory tomcat]# docker login --username=xxxxx registry.cn-beijing.aliyuncs.com

[root@lory tomcat]#docker push 镜像名：版本号
```

## Docker网络