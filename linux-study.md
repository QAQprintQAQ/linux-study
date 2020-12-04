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
##修改BaseOS库
```
vim /etc/yum.repos.d/CentOS-Base.repo 
```
注释掉mirrorlist所在行，消除对baseurl的注释，并将baseurl后面的地址改为阿里云中对应的BaseOS库地址。保存退出。
```
#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=BaseOS&infra=$infra
baseurl=https://mirrors.aliyun.com/centos/8.2.2004/BaseOS/x86_64/os/
```

##修改Extra库
```
vim /etc/yum.repos.d/CentOS-Extras.repo
```
注释掉mirrorlist所在行，消除对baseurl的注释，并将baseurl后面的地址改为阿里云中对应的extras库地址。保存退出。
```
#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
baseurl=https://mirrors.aliyun.com/centos/8.2.2004/extras/x86_64/os/
```
##更新
```
yum update
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