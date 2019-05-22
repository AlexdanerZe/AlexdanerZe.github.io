---
title: Gcmformojo使用教程
categories: 
- 工具相关
tags:
- Push

---

# Gcmformojo使用教程

能够使你关闭后台应用程序，但微信/QQ仍能收发消息

## 前提

1.需要一台服务器（gcm推送需要能翻墙服务器，mipush可用国内服务器）

2.服务器系统建议使用centos 7

3.需要开放端口 QQ：5000和微信：3000（具体操作建议自行查看各个vps提供商的开放端口操作）

4.最好会liunx一些操作，以及vim编辑器的常规操作

## 开始

### 架设相关环境（均在root权限下）

```
yum -y groupinstall "Development Tools"
```

~~~ 
yum install vim git openssl-devel perl cpan make gcc g++
~~~

## 安装项目

由于我们需要 Mojo::WebQQ 这个 Perl 语言项目作为主机端接收QQ消息的平台，同时 CentOS 7 自带 yum 源没有 Cpanm 包管理，所以接下来需要安装 Cpanm

~~~ yum install perl-App-cpanminus.noarch
yum install perl-App-cpanminus.noarch
~~~



~~~ curl -kL https://cpanmin.us | perl - App::cpanminus
curl -kL https://cpanmin.us | perl - App::cpanminus
 
官方服务器在国外，如果无法访问，可以选择下面的命令： 

curl http://share-10066126.cos.myqcloud.com/cpanm.pl|perl - App::cpanminus
~~~

安装好cpanm，我们开始安装Mojo::Webqq

~~~ 
cpanm Mojo::Webqq
~~~

注：这个安装很玄学，往往安装不是完整的，即便我是国外的vps，可以再执行以下代码，安装国内镜像

~~~ 
cpanm --mirror http://mirrors.163.com/cpan/ Mojo::Webqq
~~~

Mojo::Weixin的安装方法大致相同

~~~ cpanm Mojo::Weixin
cpanm Mojo::Weixin
~~~

~~~
cpanm --mirror http://mirrors.163.com/cpan/ Mojo::Weixin
~~~

建议再次检查依赖模块检查

webqq:

> ```
> curl -ks "https://raw.githubusercontent.com/sjdy521/Mojo-Webqq/master/script/check_dependencies.pl"|perl -
> ```

weixin:

> ```
> curl -ks "https://raw.githubusercontent.com/sjdy521/Mojo-Weixin/master/script/check_dependencies.pl"|perl -
> ```







### 设定文件

这里我们使用vim对文件进行编辑。

~~~ vim qq.pl
vim qq.pl
~~~

可能很多人不了解vim的基础操作，下面介绍一下

~~~ i      进入编辑模式
i      进入编辑模式
esc    退出编辑模式
:wq      退出编辑模式下，保存并退出
:q!      退出编辑模式下，强制退出
:w!      退出编辑模式下，强制保存，无视权限
~~~

这里给出一个 Perl 脚本文件的模板，具体内容请根据实际情况替换更改

~~~ use Mojo::Webqq;
use Mojo::Webqq;
#微信使用 use Mojo::Weixin
my $client = Mojo::Webqq->new(log_encoding=>"utf-8");
$client->load("ShowMsg");
#请根据自己所需的推送服务进行选择并删除或注释不需要的部分，填写格式请仿照 GCM 的方式填写
#以下为 GCM 推送
$client->load("GCM",data=>{
api_url => 'https://gcm-http.googleapis.com/gcm/send',
api_key=>'AIzaSyB18io0hduB_3uHxKD3XaebPCecug27ht8',
registration_ids=>["输入你自己从 GCMForMojo APP中获取到的令牌"],
allow_group=>["接收群消息的号码，如需要推送全部群消息可删除这一行，每个群号码之间使用 "", 分隔"],
#注：group这里微信是群聊名称，qq是群号码
#微信需要屏蔽公众号消息可加上 is_ban_official =>1,
ban_group=>[],
allow_discuss=>[],
ban_discuss=>[],
#此处为讨论组，填写格式同上
});
#以下为 MiPush 推送
$client->load("MiPush",data=>{
registration_ids=>[""],
allow_group=>[""],
ban_group=>[],
allow_discuss=>[],
ban_discuss=>[],
});
#以下为 HwPush 推送
$client->load("HwPush",data=>{
registration_ids=>[""],
allow_group=>[""],
ban_group=>[],
allow_discuss=>[],
ban_discuss=>[],
});
$client->load("UploadQRcode");
$client->load("Openqq",data=>{
#如果是微信改为 Openwx
listen => [{host=>"0.0.0.0",port=>5000}, ] ,
#如果是推送微信的话需要保证端口不重复，并请保证所设定的端口已经在防火墙内放行，同时需要在 APP 内设定好推送服务器的地址和端口
#微信可改端口3000
#注：host不需要改，否则发不了消息，只需要在app上改服务器。
});
#不需要 APP 内回复功能请删除以上三行（不包括被 # 号注释掉的几行）
$client->run();
~~~

保存退出后执行

~~~ perl qq.pl
perl qq.pl
~~~

这时候你的 GCMForMojo APP 应该会弹出一条检测到二维码事件的通知，点击它，使用手机端 QQ 扫描这个二维码，你的 GCMForMojo 就跑起来了

注：微信需要借助第二台设备进行扫码登陆

### 后台运行

perl进程并不会后台运行，这时候我们需要借助于screen命令

先安装screen

~~~ yum install -y screen
yum install -y screen
~~~

新建一个名为qqpush的screen窗口

~~~ 
screen -S qqpush
~~~

再执行

~~~ 
perl qq.pl
~~~

最后

~~~ 
按Ctrl +a +d
~~~

便可以退出客户端并且保持后台进行了