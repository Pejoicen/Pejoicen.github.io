---
title: "FRP+Windows远程桌面"
date: 2020-02-07T08:46:32+08:00
description: ""
draft: false
tags: ["frp"]
categories: [折腾]
---

服务器+FRP内网穿透实现连接Windows远程桌面

<!--more-->



# 服务端（以Linux为例）

1. vps 下载最新 frp 安装包，因为 vps 是 linux 系统的，所以下载 linux 安装包

   https://github.com/fatedier/frp/releases/ 中找最新的release版本下载

   > ```shell
   > cd /usr/local
   > wget https://github.com/fatedier/frp/releases/download/v0.31.2/frp_0.31.2_linux_amd64.tar.gz
   > ```

2. 解压安装包

   > ```shell
   > tar -zxvf  frp_0.31.2_linux_amd64.tar.gz
   > ```

   

3. 进入frp目录

   > ```shell
   > cd frp_0.31.2_linux_amd64
   > ```

4. 修改frps.ini文件

   > `[common]`部分是必须有的配置，其中`bind_port`是自己设定的frp服务端端口，`vhost_http_port`是自己设定的http访问端口。
   >
   > ```ini
   > # frps.ini
   > [common]
   > bind_port = 7000
   > vhost_http_port = 7001
   > ```
   >
   > 

5. 启动frp

   > ```shell
   > 在frps的路径下：
   > ./frps -c ./frps.ini
   > ```

   启动frpc后，连接后的情况

   > ```shell
   > 2020/02/08 08:12:22 [I] [service.go:152] frps tcp listen on 0.0.0.0:7000
   > 2020/02/08 08:12:22 [I] [root.go:205] start frps success
   > 2020/02/08 08:12:23 [I] [service.go:392] [d2a8cd7fe4f1eaca] client login info: ip [27.190.9.156:20450] version [0.31.2] hostname [] os [windows] arch [amd64]
   > 2020/02/08 08:12:24 [I] [tcp.go:63] [d2a8cd7fe4f1eaca] [RDP] tcp proxy listen port [3389]
   > 2020/02/08 08:12:24 [I] [control.go:445] [d2a8cd7fe4f1eaca] new proxy [RDP] success
   > 2020/02/08 08:12:24 [I] [tcp.go:63] [d2a8cd7fe4f1eaca] [ssh] tcp proxy listen port [6000]
   > 2020/02/08 08:12:24 [I] [control.go:445] [d2a8cd7fe4f1eaca] new proxy [ssh] success
   > 
   > ```
   >
   > 

6. 设置自动启动和后台运行

   * 使用systemctl来控制启动，以服务端为例。首先编辑frps.service

     ```shell
     sudo vi /lib/systemd/system/frps.service
     ```

     内容如下：

     ```ini
     [Unit]
     Description=fraps service
     After=network.target network-online.target syslog.target
     Wants=network.target network-online.target
     
     [Service]
     Type=simple
     
     #启动服务的命令（此处写你的frps的实际安装目录）
     ExecStart=/usr/local/frp_0.31.2_linux_amd64/frps -c /usr/local/frp_0.31.2_linux_amd64/frps.ini
     
     [Install]
     WantedBy=multi-user.target
     ```

     

   * 然后启动frps

     ```shell
     sudo systemctl start frps
     ```

     

   * 打开自启动

     ```shell
     sudo systemctl enable frps
     ```

     

   * 其他

     * 重启 `sudo systemctl restart frps`
     * 停止 `sudo systemctl stop frps`
     * 查看应用日志 `sudo systemctl status frps`

     







# 客户端（以Windows为例）

1. 下载对应的Windows版本frp

2. 下载后解压

3. 修改frpc.ini

   common 中填写服务器的IP和启动的端口

   [RDP]为自己新加的部分，local_port为客户端机器要开放的端口，remote_port为服务端的端口。远程桌面用的端口是3389.这样连接服务器的3389就会映射到客户端机器的3389端口了。

   > ```ini
   > [common]
   > server_addr = 199.19.104.113
   > server_port = 7000
   > 
   > [ssh]
   > type = tcp
   > local_ip = 127.0.0.1
   > local_port = 22
   > remote_port = 6000
   > 
   > [RDP]
   > type = tcp
   > local_ip = 0.0.0.0
   > local_port = 3389
   > remote_port = 3389
   > ```
   >
   > 

4. 启动frpc

5. 后续补充如何自动启动