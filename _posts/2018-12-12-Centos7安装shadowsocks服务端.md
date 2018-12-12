---
layout:     post
title:      Centos7安装shadowsocks服务端
subtitle:   网也要科学的上...
date:       2018-12-12
author:     zz
header-img: img/home-bg.jpg
catalog: true
tags:
    - VPN
---

# 安装

## 使用pip安装

### 查看是否已经安装了pip

```bash
pip -V
```

![img](https://zz-images.oss-cn-beijing.aliyuncs.com/blog/imgs/pip-v.png)

### 若没有安装pip,则执行命令进行安装

```bash
yum -y install python-pip
```

> ​    若提示找不到python-pip包,则说明没有安装epel扩展源,执行下面命令安装epel扩展源
>
> ​    `yum -y install epel-release`
>
> ​    然后执行以下命令使epel扩展源生效
>
> ​    `yum install -y yum-utils && yum-config-manager --enable epel`

### 再次执行前一条命令安装pip



### 安装完成后清除yum缓存(也可不执行)

```bash
yum clean all
```



### pip安装完成后,执行下列语句更新pip

```bash
pip install --upgrade pip
```



### 更新完毕后,执行命令安装shadowsocks:

```bash
pip install shadowsocks
```

![pip install shadowsocks](https://zz-images.oss-cn-beijing.aliyuncs.com/blog/imgs/pip-install-ssserver.png)



## 配置与启动

shadowsocks使用json文件作为配置文件,因此,创建一个json文件,文件名任意,文件内容如下:

```json
{
    "server":"0.0.0.0",
    "server_port":9999,
    "local_address": "127.0.0.1",
    "local_port":1080,
    "password":"zz",
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open": true,
    "workers":5
}
```

### 配置文件参数说明:

如果是服务端提供代理的，server的值需要写0.0.0.0 如果是使用代理的客户端，server值需要写你购买的ss账号的提供的服务器ip

| 参数          | 参数说明                                     | 参数示例         |
| ------------- | -------------------------------------------- | ---------------- |
| server        | (String)服务端IP地址                         | 0.0.0.0          |
| server_port   | (int)服务绑定的端口号                        | 9999             |
| local_address | (String)客户端IP地址(服务端此参数没用)       | 127.0.0.1        |
| local_port    | (int)客户端的端口号(服务端此参数没用)        | 1080             |
| password      | (String)客户端连接服务端时需要的密码         | zz               |
| timeout       | (int)超时等待时间                            | 300              |
| method        | (String)加密方式                             | aes-256-cfb      |
| fast_open     | (Boolean)快速启动(Linux可打开此选项)         | true/false二选一 |
| workers       | (int)启动的进程数量,每个进程占用8M内存来维持 | 5                |

### 保存文件后,执行命令启动shadowsocks服务

#### shadowsocks启动:

```bash
ssserver -c /etc/shadowsocks.json -d start
```



#### shadowsocks关闭:

```bash
ssserver -c /etc/shadowsocks.json -d stop
```



### 执行启动命令后,查看进程是否已经运行:

```bash
ps -ef | grep ssserver
```



## 官方网站(需翻墙):

[https://shadowsocks.org](https://shadowsocks.org/)



## 客户端下载地址:

### windows版:

<https://github.com/shadowsocks/shadowsocks-windows/releases>

### Android版:

<https://github.com/shadowsocks/shadowsocks-android/releases>



## 开机启动配置:

```bash
vim /etc/rc.d/rc.local
```



## linux下使用privoxy将socks转为http代理

若客户端直接使用shadowsocks客户端连接shadowsocks服务端,则不需要安装privoxy

##### 安装命令:

```bash
yum install -y privoxy
```

##### 编辑文件 vi /etc/privoxy/config 搜索 socks5t在下面添加一条转发代理ip:

```bash
forward-socks5t   /               127.0.0.1:1080 .
```

##### 然后搜索listen-address，取消注释的ip地址，或者直接新加下面的信息：

```bash
listen-address  127.0.0.1:8118
```

##### 完事之后，vim ~/.bashrc，加入下面的内容

```bash
export http_proxy=http://127.0.0.1:8118
```

##### 重新载入配置文件

```bash
source ~/.bashrc
```

##### 最后重启privoxy

```bash
systemctl restart privoxy.serivce
```

出现问题参考:

<https://www.cnblogs.com/liuxuzzz/p/5329536.html>