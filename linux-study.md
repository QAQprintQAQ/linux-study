#1.换源
* 由于CentOS 8主要使用AppStream, BaseOS, Extras, epel三个仓库，所以在这里主要配置上面三个仓库对应的配置文件，这里的三个仓库可以理解为同一个软件源中保存了不同种类软件的不同部分。

##修改AppStream库
```
sudo vim /etc/yum.repos.d/CentOS-AppStream.repo

```
注释掉mirrorlist所在行，消除对baseurl的注释，并将baseurl后面的地址改为阿里云中对应的AppStream库地址。保存退出。
```
#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=AppStream&infra=$infra
baseurl=https://mirrors.aliyun.com/centos/8.2.2004/AppStream/x86_64/os/
```
![](./assets/linux-study-1607144393893.png)

##修改BaseOS库
```
vim /etc/yum.repos.d/CentOS-Base.repo 
```
注释掉mirrorlist所在行，消除对baseurl的注释，并将baseurl后面的地址改为阿里云中对应的BaseOS库地址。保存退出。
```
#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=BaseOS&infra=$infra
baseurl=https://mirrors.aliyun.com/centos/8.2.2004/BaseOS/x86_64/os/
```
![](./assets/linux-study-1607144456290.png)

##修改Extra库
```
vim /etc/yum.repos.d/CentOS-Extras.repo
```
注释掉mirrorlist所在行，消除对baseurl的注释，并将baseurl后面的地址改为阿里云中对应的extras库地址。保存退出。
```
#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
baseurl=https://mirrors.aliyun.com/centos/8.2.2004/extras/x86_64/os/
```
![](./assets/linux-study-1607144522791.png)

##更新
```
sudo yum update
```

##下载更改epel库
1.安装epel源，安装后，在/etc/yum.repos.d目录下会多出几个epel文件
```
yum install -y https://mirrors.aliyun.com/epel/epel-release-latest-8.noarch.rpm

```
2.将 repo 配置中的地址替换为阿里云镜像站地址

* 下面用sed命令来直接更改EPEL的地址是最高效的，当然也可直接用vim打开文件来改。
```
sed -i 's|^#baseurl=https://download.fedoraproject.org/pub|baseurl=https://mirrors.aliyun.com|' /etc/yum.repos.d/epel*
sed -i 's|^metalink|#metalink|' /etc/yum.repos.d/epel*
```

#docker
##一、安装dockers
* 基于centos 8 的普通用户

1.卸载旧版本

```
$ sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```
2.安装yum-utils软件包（提供yum-config-manager 实用程序）并设置稳定的存储库。

```
sudo yum install -y yum-utils

sudo yum-config-manager  --add-repo   https://download.docker.com/linux/centos/docker-ce.repo

或

sudo yum install -y yum-utils
sudo yum-config-manager  --add-repo   http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

3.更新软件包索引
```
sudo yum makecache
```
4.先装新版的 containerd.io
```
sudo dnf install https://download.docker.com/linux/centos/7/x86_64/stable/Packages/containerd.io-1.2.6-3.3.el7.x86_64.rpm
```

5.安装最新版本的Docker Engine和容器

```
//docker-ce表示下载的是社区版
sudo yum install docker-ce docker-ce-cli
```

6.查看docker版本
```
sudo docker --version
```
![](./assets/linux-study-1607144643350.png)

7.启动docker
```
sudo systemctl start docker
```

8.重启docker服务器
```
sudo systemctl restart docker
```

9.开机自启
```
sudo systemctl enable docker
```
10.查看docker版本
```
//没有两个短横线
sudo docker version
```
![](./assets/linux-study-1607144692657.png)


##二、阿里云容器镜像服务
（1）登录阿里云账号

（2）在阿里云账号管理中搜索容器镜像服务

![](./assets/linux-study-1607144905052.png)

（3）在容器镜像服务中点击镜像加速器

![](./assets/linux-study-1607145134586.png)


（4）linux中执行以下命令
```
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://bakwgtaq.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```
![](./assets/linux-study-1607145324086.png)


##三、docker工作原理
```
sudo docker run hello-world
```
![](./assets/linux-study-1607159065376.png)

###运行hello-world镜像流程图

![](./assets/linux-study-1607159137193.png)

###底层原理，端口映射
* Docker是一个Client-Server结构的系统，Docker的守护进程运行在主机上，通过Socket从客户端访问!
Docker-Server接收到Docker-Client的指令，就会执行这个命令!

![](./assets/linux-study-1607159590368.png)

###docker和vm
* docker有着比vm更少的抽象层
* docker利用的是宿主机的内核，vm需要是Guest OS
* docker新建一个容器的时候，不需要像虛拟机一样重新加载一个操作系统内核。虚拟机是加载Guest OS，而docker是利用宿主机的操作系统，省略了系统引导这个复杂的过程

![](./assets/linux-study-1607159684027.png)



##三、帮助
```
sudo docker --help
```
![](./assets/linux-study-1607145388523.png)

```
sudo docker images --help

```
![](./assets/linux-study-1607145418543.png)

```
sudo docker search --help

```
![](./assets/linux-study-1607145464505.png)


##四、镜像命令
####1.查看所有镜像
```
sudo docker images
```
![](./assets/linux-study-1607145513066.png)


```
//images的帮助
sudo docker images --help

```
![](./assets/linux-study-1607145586485.png)

```
//显示所有的镜像信息
sudo docker images -a  
```
![](./assets/linux-study-1607145610730.png)


```
//只显示镜像的id
sudo docker images -q
```
![](./assets/linux-study-1607145671948.png)

####2.搜索镜像命令
```
sudo docker search name
```

```
//查找mysql软件
sudo docker search mysql
```
![](./assets/linux-study-1607145741321.png)

![](./assets/linux-study-1607145797906.png)

####3.拉取镜像
```
sudo docker pull name

```
* 不指定版本下载镜像
```
//如果不指定mysql 的tag，那么默认是下载最新版本的mysql
sudo docker pull mysql

```

![](./assets/linux-study-1607145917354.png)

```
sudo docker pull ubuntu

```
![](./assets/linux-study-1607146256128.png)

![](./assets/linux-study-1607150589171.png)

```
sudo docker pull centos

```
![](./assets/linux-study-1607146310248.png)


* 指定版本下载
```
//下载5.7版本的mysql,版本一定要dockerhub提供！！
sudo docker pull mysql:5.7

```
![](./assets/linux-study-1607146483194.png)


####4.查看下载的镜像
```
sudo docker images
```
![](./assets/linux-study-1607146511958.png)

####5.删除镜像
* 通过镜像id移除镜像
```
//rm表示remove , i表示images, -f表示force , id表示软件镜像id
sudo docker rmi -f 镜像id
```

```
sudo docker rmi -f mysql(移除最新的mysql镜像)
```
![](./assets/linux-study-1607146620945.png)

```
sudo docker rmi -f mysql:5.7(mysql 5.7的镜像id)
```
![](./assets/linux-study-1607146648157.png)

##五、容器命令
* 有了镜像才可以创建容器，比如我们拉取了一个ubuntu的镜像，那么我们可以创建一个容器运行ubuntu。
```
sudo docker pull ubuntu
```
```
sudo docker pull centos
```
####1.创建容器并启动
```
//--it表示进入交互界面,/bin/bash进行交互
sudo docker run -it ubuntu  /bin/bash
```
![](./assets/linux-study-1607146878022.png)

```
//--it表示进入交互界面,/bin/bash进行交互
sudo docker run -it centos  /bin/bash
```
![](./assets/linux-study-1607147064022.png)


####2.容器停止退出

```
exit
```
![](./assets/linux-study-1607146953416.png)

####3.容器不停止退出(后台运行)
```
ctrl +p +q
```

####4.查看正在运行的容器(可以看见容器的id)
```
sudo docker ps

```
![](./assets/linux-study-1607147143607.png)

####5.查看正在运行的容器的id
```
sudo docker ps -q
```
![](./assets/linux-study-1607147212500.png)

####5.查看历史的运行过的容器（已经创建的容器）
```
sudo docker ps -a
```
![](./assets/linux-study-1607147255019.png)

####6.删除容器

```
sudo docker rm -f 容器id
```
```
//查看正在运行的容器id
sudo docker ps

sudo docker rm -f 43700d7d93c9

sudo docker ps
```
![](./assets/linux-study-1607147504722.png)

####7.容器的启动和停止
* run是新建一个容器(操作的是镜像)，start是启动一个已经存在的容器(操作的是容器的id)
* 当你创建并运行了exit命令，你可以通过以下启动容器命令，而不是再重新创建一个容器

（1）启动已经创建的容器
```
//查看已经创建并运行过的容器
sudo docker ps -a 
sudo docker start 容器id  //启动容器
```
```
//查看已经创建并运行过的容器
sudo docker ps -a 
//启动容器
sudo docker start 549d79c65e8d
//查看容器是否启动成功
sudo docker ps
```
![](./assets/linux-study-1607151212217.png)

（2）重启容器
```
//查看正在运行的容器
sudo docker ps
sudo docker restart 容器id  //重启容器
```
```
//查看正在运行的容器
sudo docker ps

sudo docker restart 549d79c65e8d
```
![](./assets/linux-study-1607151360761.png)


（3）停止当前容器
```
//查看正在运行的容器
sudo docker ps
sudo docker stop 容器id  //停止当前容器
sudo docker ps
```
```
//查看正在运行的容器
sudo docker ps
sudo docker stop 549d79c65e8d
sudo docker ps
```
![](./assets/linux-study-1607151543885.png)

（4）强制停止当前容器
```
//查看正在运行的容器
sudo docker ps
sudo docker kill 容器id  //强制停止当前容器
sudo docker ps
```
```
sudo docker ps
sudo docker kill 549d79c65e8d
sudo docker ps
```
![](./assets/linux-study-1607151671327.png)

##常用的其他命令
####1.后台启动容器
* 这里有一个陷阱
```
sudo docker run -d 镜像名
```
```
sudo docker run -d centos
```
![](./assets/linux-study-1607147712699.png)

* 查看一下正在运行的容器
```
sudo docker ps 
//发现centos停止了！为什么呢？？
```
![](./assets/linux-study-1607147830221.png)

* docker 容器使用后台运行，就必须要有要一个前台进程，docker 发现没有前台应用，就会自动停止。
nginx容器启动后，发现自己没有提供服务，就会立刻停止。

####2.查看日志命令
* 日志的帮助命令
```
sudo docker logs --help
```
![](./assets/linux-study-1607147872637.png)

* 显示全部日志信息
```
//-t -f表示持续显示日志，可以合在一起 -tf
sudo docker logs -t -f 容器id
或者
sudo docker logs -tf 容器id

```

```
sudo docker ps

sudo docker logs -tf 549d79c65e8d(centos的容器id)
```
![](./assets/linux-study-1607148204656.png)

* 显示10条日志信息
```
sudo docker logs -t -f --tail 10  容器id
或者
sudo docker logs -tf --tail 10  容器id

```
```
sudo docker logs -tf --tail 10 549d79c65e8d(centos的容器id)

```
![](./assets/linux-study-1607148248557.png)

####3.显示容器内的进程id
* 查看容器内部的进程id。 知道了容器内部的进程id，便可以kill容器内部的进程了
```
sudo docker top 容器id(通过sudo docker ps 命令查看的容器id)
```
```
//目的是查看容器的id
sudo docker ps 

sudo docker top 549d79c65e8d
```

![](./assets/linux-study-1607148342994.png)


####4.查看容器的元素据
```
sudo docker inspect 容器id
```

```
sudo docker inspect 549d79c65e8d(centos的容器id)
```

* 查看容器的元数据
```
//目的是查看容器的id
sudo docker ps 

sudo docker inspect 容器id
```
![](./assets/linux-study-1607148566006.png)

####5.进入当前正在后台运行的容器
（1）通过开启一个新的终端进入容器
```
//exec 表示execute
sudo docker exec -it 容器id /bin/bash
```

* 容器内部开启了一个新的进程
```
sudo docker ps 

sudo docker exec -it 549d79c65e8d  /bin/bash
```
![](./assets/linux-study-1607148879132.png)

（2）通过正在运行的终端进入容器
* 容器内部没有开启新的进程
```
//前提是有一个正在运行的终端
sudo docker attach 容器id
```

```
//前提是有一个正在运行的终端
sudo docker attach 549d79c65e8d
```
* 通过容器id进入容器
```
sudo docker ps 

sudo docker attach 549d79c65e8d
```
![](./assets/linux-study-1607149041461.png)

####6.从容器内部拷贝文件到主机
* 从主机拷贝到容器内部一般使用挂载
```
//查看docker正在运行的容器的id
sudo docker ps 
```
```
//通过开启一个新的终端进入容器内部
sudo docker exec -it 549d79c65e8d /bin/bash
```
```
//在centos容器内的home目录新建一个文件
cd /home
touch liwenli.java
```
```
//容器不停止退出(后台运行)
ctrl +p +q
```
```
//查看正在运行的容器id
sudo docker ps
```
```
//将centos容器的/home下的liwenli.java文件拷贝到主机的/home目录下
sudo docker cp 549d79c65e8d:/home/liwenli.java  /home

```
![](./assets/linux-study-1607152138687.png)

##作业
###1.部署nginx
（1）搜索nginx镜像
```
sudo docker search nginx
```
（2）拉取nginx镜像
```
sudo docker pull nginx
```
（3）运行nginx镜像
```
// -d后台运行 --name给容器命名 -p宿主机端口

sudo docker run -d --name nginx01 -p 3344:80 nginx

```

![](./assets/linux-study-1607132365858.png)

（4）在nginx容器内可以通过http://container-ip:80访问

（5）虚拟机本地centos8查看3344端口
```
sudo curl localhost:3344
```
![](./assets/linux-study-1607152716675.png)

（6）本地windows通过http://虚拟机centos8的ip:3344访问
* 首先查看虚拟机centos8的ip
```
ip addr
```
```
//虚拟机centos8的ip是 192.168.177.129
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:0c:29:96:d9:b1 brd ff:ff:ff:ff:ff:ff
    inet 192.168.177.129/24 brd 192.168.177.255 scope global noprefixroute ens33
       valid_lft forever preferred_lft forever
    inet6 fe80::6b76:8e10:e047:a3e6/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
```
* windows打开浏览器输入虚拟机centos8的ip:3344端口
```
http://192.168.177.129:3344/
```
![](./assets/linux-study-1607133183592.png)

（7）通过新建一个终端进入nginx
```
sudo docker ps

```
```
sudo docker exec -it nginx01(nginx容器名)/bf5c2a3e921a(nginx容器id)  /bin/bash
```
![](./assets/linux-study-1607153009747.png)

（8）退出并终止nginx
```
//退出终端，但是nginx还在后台运行，也就是说我们还可以看到 sudo curl localhost:3344 的html代码
exit 
```
![](./assets/linux-study-1607153088422.png)

![](./assets/linux-study-1607153064833.png)


```
sudo docker ps
sudo docker stop nginx01(nginx容器名)/bf5c2a3e921a(nginx容器id)
//stop nginx容器后就无法访问nginx了，也就是说我们无法看到 sudo curl localhost:3344 tml代码了
```
![](./assets/linux-study-1607153186082.png)
![](./assets/linux-study-1607153302352.png)

（9）总结
* 通过这几步，我们已经成功的部署了一个nginx，并且可以到公网上访问

* 思考问题:我们每次改动nginx配置文件，都需要进入容器内部？十分的麻烦，我要是可以在容器外部提供一个映射路径，达到在
容器修改文件名，容器内部就可以自动修改? -v数据卷!

###2.部署tomcat:9.0
* 官方的使用方式

```
//You can test it by visiting http://container-ip:8080 in a browser or, if you need access outside the host, on port 3355
docker run -it --rm -p 3355:8080 tomcat:9.0
```
* 我们自己的使用方式

（1）查看tomcat镜像
```
sudo docker search tomcat
```
![](./assets/linux-study-1607153446980.png)

（2）拉取tomcat:9.0
```
sudo docker pull tomcat:9.0
```
![](./assets/linux-study-1607153522974.png)

（3）启动tomcat:9.0镜像
```
//-d 表示后台运行，-p表示将host端口映射，--name表示给运行的tomcat容器命名
sudo docker run -d -p 3355:8080 --name tomcat01 tomcat:9.0
//外网可以通过http://虚拟机centos8的ip:3355访问，在容器内可以通过http://container-ip:8080访问
```
![](./assets/linux-study-1607153590679.png)

* 如果没有tomcat版本号，那么docker会去dockerhub找最新的tomcat版本并运行

（4）访问测试
* 能够通过外网访问证明tomcat已经成功的部署了，但是为社么会出现404呢？？

![](./assets/linux-study-1607137379931.png)

（5）探索出现404的原因

* 出现404的原因是下载的tomcat版本是精简版，webapps里面没有资源，所以会出现404错误
```
sudo docker exec -it tomcat01 /bin/bash

ls

cd webapps

ls
```
![](./assets/linux-study-1607153779502.png)

![](./assets/linux-study-1607138077310.png)

（6）解决404问题
* 将webapps.dist目录的文件全部复制到webapps目录下，注意一定要使用-r表示递归拷贝

![](./assets/linux-study-1607153954222.png)

![](./assets/linux-study-1607138580247.png)

* 再去外网访问tomcat，会出现tomcat的欢迎界面

![](./assets/linux-study-1607138690054.png)
（7）tomcat:9.0在docker部署完成

##commit提交容器
（1）所有的Docker镜像都起始于一个基础镜像层,当进行修改或增加新的内容时,就会在当前镜像层之上,创建新的镜像层。


![](./assets/linux-study-1607155523101.png)


（2）Docker镜像都是只读的，当容器启动时，一个新的可写层被加载到镜像的顶部! 这一层就是我们通常说的容器层，容器之下的都叫镜像层!

![](./assets/linux-study-1607156996745.png)

（3）提交容器成为一个新的镜像副本
````
sudo docker commit -m="提交的描述信息" -a="作者" 容器id 目标镜像名:[tag]
````

```
sudo docker commit -m="add webapps" -a="liwenli" 683a2c0c2078 tomcat:1.0

```
![](./assets/linux-study-1607158007405.png)

（4）总结
* 启动一个默认的tomcat
* 发现这个默认的tomcat是没有webapps应用，官方的镜像默认webapps下面是没有文件的!
* 自己将webapps.dist中的文件拷贝到webapps目录下
* 将操作过的容器通过commit提交为一个新的tomcat镜像!以后使用自己修改过的镜像即可，这就是自己的一个修改的tomcat镜像

##容器数据卷

###Docker的理念回顾
将应用和环境打包成一个镜像！数据？ 如果数据都在容器中，那么我们容器删除，数据就会丢失！需求：数据可以持久化
MySQL容器删了，删库跑路！需求：MySQL数据可以存储在本地！
容器之间可以有一个数据共享的技术！Docker容器中产生的数据同步到本地！
这就是卷技术！目录的挂载！将我们容器内的目录挂载到linux系统上。
总结：容器数据卷就是为了实现容器的持久化和同步操作，容器间也是可以数据共享的
好处：我们以后修改只需要在本地修改即可，容器内会自动同步！



![](./assets/linux-study-1607180820619.png)

###直接使用命令挂载

1.使用-v命令挂载
```
sudo docker run -it -v 主机目录:容器内目录
```
```
sudo docker run -it -v /home/liwenli/centoshome:/home centos /bin/bash

```
![](./assets/linux-study-1607182719049.png)

![](./assets/linux-study-1607183005906.png)

2.查看是否挂载成功
```
sudo docker ps
sudo docker inspect dc26736f2dc8
```
![](./assets/linux-study-1607209949869.png)
![](./assets/linux-study-1607210138874.png)

###mysql同步数据
（1）搜索镜像
```
sudo docker search mysql
```
![](./assets/linux-study-1607211652604.png)

（2）拉取镜像
```
sudo docker pull mysql
```
![](./assets/linux-study-1607211755766.png)

（3）docker官方安装
```
//官方没有进行数据挂载
sudo docker run -p 3310:3306  --name mysql01  -e MYSQL_ROOT_PASSWORD=liwenli -d mysql
```

（4）准备
* 我们首先要知道挂载数据卷的时候主机的目录的优先级是高于容器目录的，那么当我们的主机目录为空的时候容器的目录也会同步为空，这样就破坏了mysql文件。
所以我们要先将mysql要挂载的目录里面的所有文件和目录先拷贝到主机目录下，这样就不会破坏mysql被挂载的目录文件了！！

![](./assets/linux-study-1607220312793.png)

![](./assets/linux-study-1607220741160.png)


（5）创建新的mysql容器并挂载数据卷
```
//-d后台运行  -p端口映射  -V卷挂载   -e环境配置  --name容器名字

sudo docker run -d -p 3311:3306 -v /home/liwenli/mysql/conf:/etc/mysql/conf.d -v /home/liwenli/mysql/data:/var/lib/mysql --name mysql02 -e MYSQL_ROOT_PASSWORD=liwenli -d mysql
```

（6）连接mysql数据库

```
//查看虚拟机ip
sudo ip addr 
```
![](./assets/linux-study-1607221008660.png)

```
//关闭虚拟机的防火墙，或者将3310，3311端口打开
sudo systemctl stop firewalld

//查看防火墙状态
sudo systemctl status firewalld
```
![](./assets/linux-study-1607221239160.png)

```
windows 中进行远程连接
```
![](./assets/linux-study-1607221571468.png)

（7）总结
* navicat-连接到服务器的3311 --- 3311 和容器内的3306映射，这个时候我们就可以连接上虚拟机里面docker部署的mysql服务器了!

（8）补充
* 使用数据卷挂载后，mysql容器里面的数据和本机的数据是相互隔离的，也就是说我们删除了mysql容器不会影响到宿主机目录里面的数据，这样就实现了mysql容器数据的持久化，不丢失！！！

![](./assets/linux-study-1607222169218.png)


###具名挂载和匿名挂载

如何确定是具名挂载、匿名挂载还是指定路径挂载！！

####1.匿名挂载
（1）创建容器并匿名挂载
```
-v 容器内路径 #匿名挂载
```
```
sudo docker run -d -p 3388:80 --name nginx02 -v /etc/nginx nginx

```
![](./assets/linux-study-1607225763675.png)

（2）查看挂载情况
```
sudo docker volume ls

```
![](./assets/linux-study-1607226121706.png)

![](./assets/linux-study-1607226044807.png)
![](./assets/linux-study-1607226738655.png)


####2.具名挂载
（1）创建容器并具名挂载
```
-v 卷名:容器内路径 #具名挂载

```
```
sudo docker run -d -p 3399:80 --name nginx03 -v juming-nginx:/etc/nginx nginx
```
![](./assets/linux-study-1607226232622.png)

（2）查看挂载情况
```
sudo docker volume ls
```
![](./assets/linux-study-1607226374607.png)

![](./assets/linux-study-1607226338866.png)

![](./assets/linux-study-1607226758095.png)

####3.指定路径挂载
（1）准备
* 先将nginx容器中的/etc/nginx目录下面的所有文件拷贝到主机的/home/liwenli/nginx目录下

![](./assets/linux-study-1607229567624.png)

（1）创建容器并指定路径挂载
```
-v /宿主机路径:容器内路径 #指定路径挂载!
```
```
sudo docker run -d -p 3377:80 --name nginx04 -v /home/liwenli/nginx:/etc/nginx nginx

```
![](./assets/linux-study-1607229876911.png)

（2）查看挂载情况
```
sudo docker volume ls
```
![](./assets/linux-study-1607230098959.png)

![](./assets/linux-study-1607226774012.png)


####4.挂载卷存放位置
（1）匿名挂载
```
sudo docker volume ls

```
```
sudo docker volume inspect 4934e57d2780b093fc0c19ac496e1d2ad1df14abfd3c301e3ff77dfba5109647
```
![](./assets/linux-study-1607230481100.png)


（2）具名挂载存放位置
```
sudo docker volume ls

```
```
sudo docker volume inspect juming-nginx
```

![](./assets/linux-study-1607230695724.png)

（3）指定位置存放
```
sudo docker ps 

sudo docker inspect nginx04

```
![](./assets/linux-study-1607231107283.png)
![](./assets/linux-study-1607230987189.png)


####5.拓展

通过-v容器内路径ro、rw改变读写权限
```
ro     readonly #只读
rw     readwrite # 可读可写

```
一旦这个了设置了容器权限，容器对我们挂载出来的内容就有限定了!
```
docker run -d -p 3333:80 --name nginx05 -v juming-nginx:/etc/nginx:ro nginx
docker run -d -p 3333:80 --name nginx05 -v juming-nginx:/etc/nginx:rw nginx
```
只要看到ro就说明这个路径只能通过宿主机来操作，容器内部是无法操作!


###通过DockerFile挂载
* 先制作一个centos镜像

（1）创建一个目录

```
//在/home/liwenli/目录下新建一个docker-volumes目录
mkdir docker-volumes
```
![](./assets/linux-study-1607250587190.png)

（2）编写制作镜像的构建文件

* docker镜像构建文件dockerfile中，以'#' 开头的一行将被视为注释，出现在其他位置视为参数。也就不难理解报错原因：将写在同一行的注释视为参数了。
```
vim dockerfile 
```
```

#Dockerfile是一个用来构建docker镜像的构建文件
#基础镜像
FROM centos
#匿名挂载数据卷
VOLUME ["volume01","volume02"]
#进入控制台
CMD /bin/bash


```
![](./assets/linux-study-1607252591554.png)



（3）执行镜像构建文件
```
//-f（file）表示docker镜像构建文件名  -t（target）表示目标的docker镜像名  最后有一个"."不要少!!
sudo docker build -f dockerfile -t liwenli-centos .

```
![](./assets/linux-study-1607252712851.png)
（4）查看生成的镜像
```
sudo docker images
```
![](./assets/linux-study-1607253075157.png)

（5）启动镜像
```
sudo docker run -it 5aaae513b625 /bin/bash
```
![](./assets/linux-study-1607253476310.png)

（6）查看挂载情况
```
sudo docker ps 
sudo docker inspect 3b87b6c266c3
```
![](./assets/linux-study-1607254382073.png)

![](./assets/linux-study-1607254110989.png)

（7）查看是否可以同步
* 在centos容器里面创建文件，主机能够同步

![](./assets/linux-study-1607254972967.png)

* 在主机里面修改文件，centos容器里面能够同步

![](./assets/linux-study-1607255282379.png)

###数据卷容器
####（1）基本知识
![](./assets/linux-study-1607255553865.png)

####（2）创建一个父容器centos01
```
sudo docker run -it --name centos01 liwenli-centos
```
![](./assets/linux-study-1607256782706.png)
####（3）创建一个子容器centos02
```
sudo docker run -it --name centos02 --volumes-from centos01 liwenli-centos
```
![](./assets/linux-study-1607257408633.png)

####（4）检测同步情况
* centos01中的volume01中创建的centos01.java在centos02中的volume01中能够同步
![](./assets/linux-study-1607258152313.png)

* centos02中的volume01中创建的centos02.java在centos01中的volume01中能够同步
![](./assets/linux-study-1607258506851.png)

* 总结，数据卷容器能够实现容器之间的数据共享
####（5）探究数据卷容器共享数据的根本原因
* centos01和centos02的volume01，volume02分别挂载在宿主机的同一个目录下，所以能够实现数据共享！
![](./assets/linux-study-1607258874217.png)

![](./assets/linux-study-1607259341531.png)


##DockerFile构建镜像
* Dockerfile 就是用来构建docker镜像的构建文件
###基础知识

![](./assets/linux-study-1607245033699.png)

####（1）前提
* 每个保留关键字(指令)都是必须是大写字母

* 执行从上到下顺序执行

* docker镜像构建文件dockerfile中，以'#' 开头的一行将被视为注释，出现在其他位置视为参数。也就不难理解报错原因：将写在同一行的注释视为参数了。


* 每一个指令都会创建提交一个新的镜像层，并提交！

* dockerfile是面向开发的，我们以后要发布项目做镜像就需要编写dockerfile文件，这个文件十分简单！

* Docker镜像逐渐成为企业交付的标准，必须要掌握！

####（2）步骤（开发，部署，运维！）
* DockerFile：构建文件，定义了一切的步骤源代码
* Dockerlmages：通过DockerFile构建生成的镜像，最终发布和运行的产品！
* Docker容器：容器就是镜像运行起来提供服务器



###dockerfile指令
####（1）FROM #基础镜镜像，一切从这里开始构建

####（2）MAINTAINER #镜像是谁写的，姓名+邮箱

####（3）RUN #镜像构建的时候需要运行的命令

####（4）ADD #步骤: tomcat镜像，这个tomcat压缩包!添加内容

####（5）WORKDIR #镜像的工作目录

####（6）VOLUME #挂载的目录

####（7）EXPOSE #暴露端口配置

####（8）CMD #指定这个容器启动的时候要运行的命令，只有最后一个会生效，可被替代

####（9）ENTRYPOINT #指定这个容器启动的时候要运行的命令，可以追加命令

####（10）ONBUILD #当构建一个被继承DockerFile 这个时候就会运行ONBUILD 的指令。触发指令。

####（11）COPY #类似ADD，将我们文件拷贝到镜像中

####（12）ENV #构建的时候设置环境变量!

###创建自己的centos镜像
####1.编写docker镜像构建文件
