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

##### 安装php

```
php -v //查看php版本
sudo apt install php7.4-cli  //树莓派ubuntu服务器php安装命令
sudo apt-get install php  //输入该命令后 安装了apache
```

Apr 26 10:04:32 ubuntu systemd[1]: apache2.service: Failed with result 'exit-cod                 e'.
Apr 26 10:04:32 ubuntu systemd[1]: Failed to start The Apache HTTP Server.



## 添加 PHP 7.3 PPA

```php
$ sudo add-apt-repository ppa:ondrej/php
$ sudo apt-get update
```

安装php扩展

```

sudo apt-get install nginx php7.3-fpm php7.3-cli php7.3-curl php7.3-gd php7.3-cgi
```

重启php

```
sudo service php7.3-fpm restart
```

```
sudo nano /etc/nginx/sites-available/default
```

将其中的如下内容

备份default

```
location / {
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                try_files $uri $uri/ =404;
        }
```

替换为

```
location / {
			index  index.html index.htm index.php default.html 	default.htm default.php;
}
 
location ~\.php$ {
	fastcgi_pass unix:/run/php/php7.3-fpm.sock;
	#fastcgi_pass 127.0.0.1:9000;
	fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
	include fastcgi_params;
}
```

测试php

```
默认页面通常位于/var/www/html文件夹
现在删除index.html文件：sudo rm index.html
并创建文件index.php：sudo touch index.php
编辑index.php:sudo nano index.php
输入：<?php echo "hello world"; ?>
```

![image-20210426190829473](https://i.loli.net/2021/04/26/pDJK74RtVWqsfnB.png)

#### 2.部署 Pi Dashboard

**GitHub 部署**
如果你了解过 GitHub 的基本操作，通过 GitHub 来下载本项目到 Pi 上会相当方便。

```
#如果已安装过 git 客户端可以跳过下一行
sudo apt-get install git
cd /var/www/html
sudo git clone https://github.com/nxez/pi-dashboard.git
```

即可通过 `http://树莓派IP/pi-dashboard` 访问部署好了的 Pi Dashboard。

同样如果页面无法显示，可以尝试在树莓派终端给源码添加运行权限，例如你上传之后的路径是 `/var/www/html/pi-dashboard`，则运行。

```
cd /var/www/html
sudo chown -R www-data pi-dashboard
```

![image-20210426190756223](https://i.loli.net/2021/04/26/WjbGNV9Oqr3gi4v.png)