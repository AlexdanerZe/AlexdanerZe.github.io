---
title: Fcmformojo使用教程
categories: 
- 工具相关
tags:
- Push
---

# Fcmformojo使用教程

能够使你关闭后台应用程序，但微信/QQ仍能收发消息

## 前提

1.需要一台服务器

2.服务器系统建议使用centos 7

3.需要开放端口 5003/5004/5005（具体操作建议自行查看各个vps提供商的开放端口操作）

4.最好会liunx一些操作，以及vim编辑器的常规操作

## 开始

###  假设相关环境

```
yum -y groupinstall "Development Tools"
```

```
yum install vim git openssl-devel perl cpan make gcc g++
```

安装EPEL 仓库

~~~ 
yum install epel-release
~~~

安装nodejs

~~~ 
yum install nodejs
~~~

安装npm

~~~ 
yum install npm
~~~

安装git

~~~ 
yum install git
~~~



## 安装项目

由于我们需要 Mojo::WebQQ 这个 Perl 语言项目作为主机端接收QQ消息的平台，同时 CentOS 7 自带 yum 源没有 Cpanm 包管理，所以接下来需要安装 Cpanm

```yum install perl-App-cpanminus.noarch
yum install perl-App-cpanminus.noarch
```

```curl -kL https://cpanmin.us | perl - App::cpanminus
curl -kL https://cpanmin.us | perl - App::cpanminus
 
官方服务器在国外，如果无法访问，可以选择下面的命令： 

curl http://share-10066126.cos.myqcloud.com/cpanm.pl|perl - App::cpanminus
```

安装好cpanm，我们开始安装Mojo::Webqq

```
cpanm Mojo::Webqq
```

注：这个安装很玄学，往往安装不是完整的，即便我是国外的vps，可以再执行以下代码，安装国内镜像

```
cpanm --mirror http://mirrors.163.com/cpan/ Mojo::Webqq
```

安装拓展：

```
yum install -y perl-Crypt-OpenSSL-RSA perl-Crypt-OpenSSL-Bignum
```

建议再次检查依赖模块检查

webqq:

> ```
> curl -ks "https://raw.githubusercontent.com/sjdy521/Mojo-Webqq/master/script/check_dependencies.pl"|perl -
> ```

##### 下载服务端

执行下面几条命令来下载服务端并安装所需的 node 依赖。

~~~
git clone https://github.com/RikkaApps/FCM-for-Mojo-Server.git
cd FCM-for-Mojo-Server
cp config.example.js config.js
npm install
~~~

运行

> 为避免错过二维码扫描通知而不知所措，建议在运行前先完成客户端配置的一部分（填写好服务器 URL）。

~~~
npm start
~~~



