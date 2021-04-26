# 树莓派配置Ubuntu服务器

## 一、初始化

1、[Install Ubuntu on a Raspberry Pi | Ubuntu](https://ubuntu.com/download/raspberry-pi)下载镜像

2、balenaEtcher刻录镜像，sd卡容量大于4G

3、修改systemboot中network-config文件配置WiFi

严格遵循空格缩进规则，注意":"后一定添加空格，且注意WiFi ssid 名称末尾带有空格

4、上电，等待开机，查看路由器后台树莓派ip,使用ssh连接该ip,路由器后台没有ip请返回修改WiFi配置，本人使用网线连接路由器，配置许久才配置WiFi成功

5、使用默认账号ubuntu 密码ubuntu 登录服务器



## 二、配置开源Pi Dashboard (Pi 仪表盘)检测设备运行状态

### 安装方法

安装共2步，首先安装 Nginx（或 Apache）和 PHP。然后在 Nginx 目录通过 SFTP 或 GitHub 部署好本项目的程序。

#### 1.安装 Nginx 和 PHP

在 Pi 的终端运行以下命令。安装Nginx

```
sudo apt-get update
sudo apt-get install nginx
sudo service nginx start
```

浏览器输入树莓派ip出现niginx初始页面，证明nginx服务启用成功

![](https://i.loli.net/2021/04/26/JLFvWT9y35efnSH.png)

小提示：linux查看ip地址命令为 ifconfig

ubuntu服务器需先安装网络工具才能使用

```
sudo apt-get update

sudo apt-get install net-tools
```

安装php

