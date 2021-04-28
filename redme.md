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









## 三、nginx支持cgi（实现web页面关机和重启）

[Nginx配置CGI_、moddemod-CSDN博客](https://moddemod.blog.csdn.net/article/details/106885761?utm_medium=distribute.pc_relevant.none-task-blog-2~default~BlogCommendFromBaidu~default-10.control&dist_request_id=1332049.20056.16194934970974617&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2~default~BlogCommendFromBaidu~default-10.control)



### 1、fcgiwrap的安装和配置



```
# sudo apt-get install fcgiwrap 

由于源没有fcgiwrap，所以需要自行下载源码安装 

git clone https://github.com/gnosek/fcgiwrap.git
cd ./fcgirwap
autoreconf -i
./configure
make
make install
```

输入autoreconf -i提示需要安装

```
sudo apt install autoconf
autoreconf -i
```



```
./configure
```

![image-20210427092450608](https://i.loli.net/2021/04/27/IbadRNxefg62BFV.png)

安装gcc

```
sudo apt install gcc
```



./configure,



![image-20210427093908863](https://i.loli.net/2021/04/27/kAe4wxXURPYKNCD.png)

首先确认pkg-config工具有么有安装好，没有的话安装一下：

```bash
apt-get install -y pkg-config
```

如果已经安装那么应该是执行configure的时候漏了一步
请加下面的环境路径执行

```bash
ACLOCAL_PATH=/usr/share/aclocal ./bootstrap.sh
```

代码： [全选](https://forum.ubuntu.org.cn/viewtopic.php?t=488191#)

```
$ aclocal --print-ac-dir
/usr/share/aclocal
```

代码： [全选](https://forum.ubuntu.org.cn/viewtopic.php?t=488191#)

```
$ find /usr -name pkg.m4
/usr/share/aclocal/pkg.m4
```

pkg-config运行正常。

代码： [全选](https://forum.ubuntu.org.cn/viewtopic.php?t=488191#)

```
$ pkg-config --version
0.29.2

$ pkg-config --modversion ibus-table
1.9.14
```

![image-20210427094652457](https://i.loli.net/2021/04/27/c146wlhz3Pdu9mO.png)



```
sudo apt-get install ibus-table
```

dpkg亦能正常显示信息

代码： [全选](https://forum.ubuntu.org.cn/viewtopic.php?t=488191#)

```
$ dpkg -l pkg-config
ii  pkg-config     0.29.1-0ubun amd64        manage compile and link flags for
```

输入make提示安装 ，已安装忽略该操作

```
sudo apt install make
```

## 2、上边内容配置无效

**使用Fcgiwrap**

Fcgiqwrap是另外一个CGI封装库，跟Simple CGI类似***\*。\****

***\**\*安装fcgiwrap\*\**\***

```
apt-get install fcgiwrap
```

安装以后fcgiwrap默认已经启动，对应的套接字是 /var/run/fcgiwrap.socket 。如果没有启动，使用 /etc/init.d/fcgiwrap 手动启动。



**配置nginx的vhost文件**

在要支持cgi脚本的路径下，添加对应的server配置。比如所有的cgi都在cgi-bin路径下（现在/monitor/api/下）：



```
	server {
        listen 80;
        server_name www.a.com;
        charset UTF-8;
        access_log /data/logs/www.a.com.log;
        root /data/web/www;


        location / {
          index index.html index.htm test.php;
        }

        location ~ ^/monitor/api/ {
          gzip off;
          fastcgi_pass unix:/var/run/fcgiwrap.socket;
          include /etc/nginx/fastcgi_params;
          fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        }



}
```

**重新加载nginx：**



```
/etc/init.d/nginx reload
```





**web测试页面下载：**



```
https://pan.baidu.com/s/16GGRrXRCY-oUiuXxr6J_xQ
提取码：9t1y
```

**创建文件夹和设置权限**

```
mkdir /data/web/www
mkdir /data/logs
chmod +x /data/web/www/monitor/api/*.sh
chmod 777 /var/run/fcgiwrap.socket
```

**打开页面测试**



参考[在ubuntu下为nginx配置支持cgi脚本的方案_weixin_34402408的博客-CSDN博客](https://blog.csdn.net/weixin_34402408/article/details/92476140)

2、nginx的配置
# 在/etc/nginx/conf.d/内增加自己的针对cgi程序的配置，配置如下

     server {
         listen                80;
         server_name           192.168.1.61;
         root                  /var/www/cgi-bin/cgit-data;
         try_files             $uri @cgit;
     location / {
         	fastcgi_param       SCRIPT_FILENAME /var/www/cgi-bin/cgit;
         	#fastcgi_index       cgit;
         	fastcgi_param       PATH_INFO       $uri;
         	fastcgi_param       QUERY_STRING    $args;
         	fastcgi_param       HTTP_HOST       $server_name;
         	fastcgi_pass        127.0.0.1:8082;                                                                                         
         	include             fastcgi_params;
    	}
    }



# 四、配置花生壳进行内网穿透





## 1、下载

https://hsk.oray.com/download/

选择树莓派64位





## 2、上传到树莓派

```
pscp -r phtunnel_5_0_rapi_aarch64.deb ubuntu@192.168.1.61:/home/ubuntu/downloads
```





## 3、安装

```
 sudo dpkg -i phtunnel_5_0_rapi_aarch64.deb
```

安装成功后，将显示花生壳的SN码、默认密码（admin）及远程管理地址http://b.oray.com。

![image-20210428113650774](https://i.loli.net/2021/04/28/2KabM769QZsX5ER.png)





## 4、操作功能

1、输入命令：phddns回车，可以看到扩展功能**。phddns start（启动）| status（状态）| stop（停止）|restart（重启）| reset（重置）|enable（开机自启动）|disable（关闭开机自启动）|version（版本）**

![image-20210428113954099](https://i.loli.net/2021/04/28/tOXqLYrj6z9hGfa.png)





## 5、日志文件存放路径：**/var/log/phddns**



![image-20210428115021811](https://i.loli.net/2021/04/28/3UdYxOuADqBmnoe.png)





## 6、卸载命令：**sudo dpkg -r phddns**

