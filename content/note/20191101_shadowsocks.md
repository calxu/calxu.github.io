---
title:  '利用开源ss工具快速搭建翻墙VPN'
date: '2019-11-01'
categories:
  - VPN
tags:
  - VPN
---

搭建ss的方法，网上有很多，请自行Google。
这里提供下链接[ss官网的搭建方法](https://github.com/shadowsocks/shadowsocks/wiki/Shadowsocks-%E4%BD%BF%E7%94%A8%E8%AF%B4%E6%98%8E)，
目前GFW已经具备识别ss流量的能力，但每次好像都是国家大事件时才进行封堵，但封堵时也误杀了很多正常的流量的ip。
所以过了这段关键时期，GFW会放行，这就需要重新搭建ss。

这篇文章主要记录下**搭建ss翻墙软件的最小配置**，方便自己在GFW封杀后再快速搭建翻墙的VPN。


## 客户端配置

### 客户端软件下载

1. [mac下载](https://github.com/shadowsocks/ShadowsocksX-NG/releases)

2. [windows下载](https://github.com/shadowsocks/shadowsocks-windows/releases)

* 注：下载最新的Assets即可


### 客户端配置

这里提供下我的ss帐号密码供大家使用，别薅我流量，黑客也不要攻击我机器，VPN仅供程序员学习使用，不能作恶；
ip可能会被GFW封，被封后我再重新切换下ip，关注这个blog即可。
**之前的协议已在2月初被封，目前已经Change端口号和加密方法，更改后如下：**

```
地址：    47.240.10.78:8765
加密方法： aes-256-ctr
密码：    Dont_be_evil
备注：    Dont_be_evil
```

注：香港的服务器，经常存在数据分发不稳定的情况，勉强用吧，后期有空会深入研究下ss后再改进。

### 客户端的工作原理（MAC版）：

启动软件会启动3个子程序，可通过 "ps aux | grep -i shadowsocks" 查看到 ShadowsocksX-NG、ss-local 和 privoxy 三个子程序；
与此同时，MAC中网络>高级>代理>自动代理配置 会被配置为软件自带的pac文件；

1. ShadowsocksX-NG 只是一个Swift写的一个GUI壳子；

2. ss-local为编译好了的程序，ss-local默认监听1080端口（可设置），接收socks协议的请求并转发至服务端；

3. privoxy是对http协议的代理，监听1087端口，然后转发到1080端口由ss-local程序负责转发；

注：以上原理详情可参考Github [ShadowsocksX-NG官网](https://github.com/shadowsocks/ShadowsocksX-NG)


## 服务端配置

### 购买VPS

1. 这里我购买的是[阿里云香港区](https://www.aliyun.com/product/swas?spm=5176.12825654.eofdhaal5.3.37b02c4aPPJcjo&aly_as=kKcMBhh5)，供参考；


### 配置server端环境


#### 安装环境：

```
$ apt install python3-pip

$ pip3 install shadowsocks
```


#### 配置文件：

```
$ cat /etc/shadowsocks.json
{
    "server":"0.0.0.0",
    "server_port":8432,
    "local_address": "127.0.0.1",
    "local_port":1080,
    "password":"Dont_be_evil",
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open": false
}
```

- 注：1.没有该文件则新建；2. server 直接填 0.0.0.0 即可；


#### 后台启动ss服务

```
$ ssserver -c /etc/shadowsocks.json -d start
```

- 注：1. 环境问题可能会报错，参考下面参考文献；2. 阿里云防火墙策略需要开放8432端口TCP协议通行；



## 参考文献

- [1] Github [Shadowsocks 服务端配置](https://github.com/shadowsocks/shadowsocks/wiki/Shadowsocks-%E4%BD%BF%E7%94%A8%E8%AF%B4%E6%98%8E)

- [2] Github [ShadowsocksX-NG客户端代码仓库](https://github.com/shadowsocks/ShadowsocksX-NG)

- [3] Github [EVP_CIPHER_CTX_cleanup报错](https://floperry.github.io/2019/02/24/2018-06-25-Ubuntu-18.04-%E4%B8%8B%E8%A7%A3%E5%86%B3-shadowsocks-%E6%9C%8D%E5%8A%A1%E6%8A%A5%E9%94%99%E9%97%AE%E9%A2%98/)
