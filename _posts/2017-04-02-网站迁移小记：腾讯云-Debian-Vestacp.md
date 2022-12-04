---
title: 网站迁移小记：腾讯云+Debian+Vestacp
layout: post
categories: [博客]
abbrlink: c2808f2a
date: 2017-04-02 01:11:05
excerpt: 使用Vestacp面板搭建独立博客的笔记。
---

先贴一张文章大纲。

![structure]({{ site.baseurl }}/user-imgs/Website-Migeration/structure.png)

这是一个樱花开得正好但我很蛋疼的下午。

中午抢到了腾讯的校园优惠，便打算把网站`ddlee.cc`迁到国内的服务器上来。

### 密码管理
先谈密码管理。

建站会涉及设置很多密码，之前明文保存在云笔记里的方案总觉得又土又笨，何况很多密码最好要随机生成，密码管理服务还是必要的。

搜索之后，我选择的是[lastPass](https://www.lastpass.com/2)。主要考虑了免费和跨平台的特性。有更高要求的建议选择付费的[1Password](https://1password.com/)。

需要安装Chrome插件和Ubuntu下的deb包，添加Secure Note的功能深得我心，也支持自定义模板。

### 主机
腾讯云的校园优惠力度很大。阿里云是9.9块/月，腾讯用完券1块/月。

这里多讲一句，学生真是幸福得不得了。GitHub Education Pack中既有有Digital Ocean的优惠，AWS也有150刀的礼品卡，Jetbeans大部分产品免费……这还不提学校里买的License。

腾讯的主机1核CPU，2G内存，20G系统盘（Linux），挂个网站还算够用。

#### SSH Key 配置
建议在配置主机前创建一个SSH Key，这样访问起来安全又省心。

Linux系统下，在`~/.ssh/`下新建`config`，写入如下类似内容：

```
Host Name
  HostName Host_IP
  User root
  IdentityFile path/to/ssh_private_key
```

这样就可以通过命令`ssh Name`直接访问主机。

#### 系统选择
建议选择Linux主机。具体哪一系可自行选择，我的选择是Debian，CentOS也是个不错的选择。

#### 安全组设置
建议先只开启用于SSH的22端口，之后再开HTTP访问的80端口，FTP的20,21端口和主机面板所用端口。

如果个人有代理服务器的话，也可以限制一下来源IP，这样可以通过登入代理服务器，在代理服务器上通过SSH登入WEB主机，需要迁移下SSH Private Key，可以通过命令`scp usr1@host1:/path1 usr2@host2:/path2`实现。

#### 网络环境
LNMP和LAMP是两种流行的结构。可以分别安装，再配置相应的`config`，也可以搜索得到很多一键安装脚本。另一种方案是用Docker部署。

我懒而笨，选择的是用主机面板一键安装。

#### 主机面板
在此之前，一直用的是AMH的免费4.2版本，简洁轻巧，功能也够用。付费版推出后，免费版遭到冷落，几乎没有更新，这如何能忍。

说起主机面板，我的启蒙是WDCP，其远古风格的UI仍历历在目，后来听说爆出漏洞，但那时我已转战AMH。

一番艰苦卓绝的搜索之后（其实就是检索了'best host control pannel'），我选择了[Vestacp](https://vestacp.com/)。

UI漂亮，功能不缺（建站，MAIL，备份），GitHub还算活跃，就决定是你了。

缺点是文件管理器收费，不能通过WEB管理文件。安装过程持续蛮久（半个小时，当然也包括了新主机系统包更新的时间）。

安装时注意Hostname填写IP或者已经配置好DNS解析的域名（如`admin.ddlee.cc`）。8083是管理面板的端口，记得在主机提供商的安全组里开放一下。

##### 建站
Vestacp支持多人管理，User身份由Package定义，安装过程会自动新建admin，拥有最高权限。

在User的设置里，可以配置用户的Package，而Package的设置里，可以配置每一用户身份的建站模板，资源上限等。如图。

![Package]({{ site.baseurl }}/user-imgs/Website-Migeration/Package.png)

建站相当容易，注意在高级选项里添加FTP账户，用于之后上传HTML文件。

##### FTP
建站完成后，记得配置好DNS解析，开放20和21端口，就可以用FileZilla测试链接。

注意，在高级选项里配置好Default local directory，设置Default remote directory为`/public_html`，并启用synchronized browsing和directory comparison，以后的FTP生活会很幸福。

##### Mail
若在建站时勾选了Mail support，可以建立个性化的邮箱名，可以设置自动回复/转发，也可以用Gmail托管。以后留邮箱的时候可以短短的了呢。

##### 配置SSL
这是无意发现的技能。

本来在我的印象里，SSL证书都是要收费的。但留心的朋友可能注意到，建站时SSL support下有Lets Encrypt Support。这一服务可以用上免费的SSL。

官网：[Let's Encrypt](https://letsencrypt.org/)

要利用这项服务，需要证明自己对网站的至高无上不可侵犯的神圣权利，方法之一是运行支持[ACME protocol]的Client，官网推荐了[Cerbot](https://certbot.eff.org/)。

在Cerbot主页可以选择自己的操作系统，会有详细的步骤，在此不表。

下面谈两个问题，一是强制重定向至HTTPS，二是取消管理端口的HTTPS。

###### 强制HTTPS
Vestacp的架构是用nginx做proxy，Apache2做HTTP Server，首先下载nginx template（proxy template）：

```bash
cd /usr/local/vesta/data/templates/web
wget http://c.vestacp.com/0.9.8/rhel/force-https/nginx.tar.gz
tar -xzvf nginx.tar.gz
rm -f nginx.tar.gz
```
之后在Package配置里，将proxy template配置为force-https，这样，身份由相应Package定义的用户建站时，proxy template就是用的强制HTTPS版本了。

###### 取消管理端口的SSL
用chrome访问管理页面时，会有Unsecure的警告，这里的SSL在`/usr/local/vesta/nginx/conf/nginx.conf`中配置。找到
```
# Vhost
server {
    listen          8083;
    server_name     _;
    root            /usr/local/vesta/web;
    charset         utf-8;

    # Fix error "The plain HTTP request was sent to HTTPS port"
    error_page      497 https://$host:$server_port$request_uri;

    # ssl                  on;
    # ssl_certificate      /usr/local/vesta/ssl/certificate.crt;
    # ssl_certificate_key  /usr/local/vesta/ssl/certificate.key;
    # ssl_session_cache    shared:SSL:10m;
    # ssl_session_timeout  10m;
```

将配置SSL的几行注释掉即可。顺便，管理页面的端口也可以在这里更改。之后运行`service vesta restart`重启服务。

### 域名与DNS
最后简单提一下域名注册跟DNS。要注意的几个点：
- 国内域名注册要备案，很烦，但cn域名好便宜。
- 在域名注册商那里配置DNS解析服务器（万网、DNSPod都好，不一定用自建网站的DNS）
- 在DNS服务商那里添加解析记录，顺便开启监控

### 拾遗
域名备案的时候，需要签一张备案单。方案是在纸上签字后调背景为透明，用Adobe PDF Reader的签字功能签好PDF，再转成JPG。

几项操作都可以通过在线工具完成，低碳生活，人人有责。


#### 总结
- 建站过程本身就其乐无穷，教程一抓一大把，难的在TROUBLE SHOOTING，所以Google是最好的伴侣。

- 命令行、vi编辑、必要的WEB知识等是基础。


@[ddlee](https://ddlee.cc)
