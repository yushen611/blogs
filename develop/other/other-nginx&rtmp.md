---
title: 'nginx+rtmp流媒体服务器搭建'
date: 2023-06-19 12:17:56
tags: [other]
published: true
hideInList: true
feature: 
isTop: false
---
<meta name="referrer" content="no-referrer"/>


# 一、概述

## 流程概述

目标是要做**直播**或者**流媒体播放**。

**整体架构**是：在客户端A向服务器B发送视频流(这一过程称之为**推流**)，另一个客户端C从服务器拉取视频流（这一过程称之为**拉流**）进行播放。

使用的技术：

* 协议采用的是rtmp协议
* 服务器使用的是nginx与nginx-rtmp模块
* 推流使用的是FFmpeg工具
* 拉流使用的是vlc软件



更多关于rtmp协议 与 FFmpeg vlc 原理的内容未来慢慢补充。



# 二、搭建服务器

这里主要是将怎么把nginx的插件nginx-rtmp模块与nginx，分别下载，并绑定起来。以及如何修改nginx配置文件，启动rtmp服务

说明：我的服务器环境是centos7

## **2.1 安装nginx和nginx-rtmp**

1.安装从源代码编译Nginx和Nginx-RTMP所需的工具。

```bash
sudo yum install pcre pcre-devel openssl openssl-devel zlib zlib-devel -y
```



2.创建一个工作目录并切换到该目录。

```bash
mkdir ~/working
cd ~/working
```



3.下载Nginx和Nginx-RTMP源。

```bash
wget http://nginx.org/download/nginx-1.9.7.tar.gz
wget https://github.com/arut/nginx-rtmp-module/archive/master.zip
```

Nginx-RTMP官方源码：https://github.com/arut/nginx-rtmp-module

安装解压缩包。

```bash
sudo yum install unzip
```



4.提取Nginx和Nginx-RTMP源。

```bash
tar -xvf nginx-1.9.7.tar.gz
unzip nginx-rtmp-module-master.zip
```



5.切换到Nginx目录。

```bash
cd nginx-1.9.7
```



6.添加模块以编译到Nginx中。包括Nginx-RTMP。

```bash
./configure --add-module=../nginx-rtmp-module-master/
```



7.使用Nginx-RTMP编译并安装Nginx。

```bash
make
sudo make install
```



我们执行命令，启动nginx服务器：

```bash
/usr/local/nginx/sbin/nginx
```



启动后，浏览器输入服务器ip，出现下图页面表示nginx运行成功

![image-20230429185906074](https://gitee.com/yushen611/img/raw/master/image-20230429185906074.png)

## 2.2 修改nginx配置

把`/root/working/nginx-1.9.7/conf`里的`nginx.conf`文件打开，在最下面加一个rtmp内容：

```
rtmp
{
    server
    {
        listen 1935;
        chunk_size 4096;
        application live
        {
            live on;
        }
    }
}
```

在http中加一个内容，使nginx能具有直播状态监听的功能(**这个功能没测试，但不影响正常的推拉流功能**)。其中/root/working/nginx-rtmp-module-master是博主安装的nginx-rtmp-module的绝对路径，各位得根据自己安装的nginx-rtmp-module的路径进行修改。8080是拉流请求的端口号。

```
 server {
        listen       8080;
        location /stat{
                rtmp_stat all;
                rtmp_stat_stylesheet stat.xsl;
        }
        location /stat.xsl{
               root /root/working/nginx-rtmp-module-master;
        }
    }
```

总结：改完后的内容如下：

```

#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}

rtmp
{
    server
    {
        listen 1935;
        chunk_size 4096;
        application live
        {
            live on;
        }
    }
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       8080;
        location /stat{
                rtmp_stat all;
                rtmp_stat_stylesheet stat.xsl;
        }
        location /stat.xsl{
               root /root/working/nginx-rtmp-module-master;
        }
    }
    server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            index  index.html index.htm;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

}

```



## 2.3加载nginx新配置，并重启

先关闭nginx

```bash
/usr/local/nginx/sbin/nginx -s stop
```

再设置nginx配置文件路径，并检查一下配置文件是否格式正确

```bash
/usr/local/nginx/sbin/nginx -c /root/working/nginx-1.9.7/conf/nginx.conf
sudo ./nginx -t
```

如果输出如下，表示配置文件格式正确：

![image-20230429191456620](https://gitee.com/yushen611/img/raw/master/image-20230429191456620.png)

最后重新加载nginx即可

```bash
/usr/local/nginx/sbin/nginx -s reload
```



**检查部分**：查看所有nginx监听的端口

```bash
sudo lsof -i -P -n | grep nginx
```

如果有监听1935端口，表示监听成功，如图：

<img src="https://gitee.com/yushen611/img/raw/master/image-20230429191224807.png" alt="image-20230429191224807" style="zoom:80%;" />

如果只有80端口（如图）表示未配置成功

<img src="https://gitee.com/yushen611/img/raw/master/image-20230429191303946.png" alt="image-20230429191303946" style="zoom:80%;" />

**防火墙部分**：

请检查服务器有关端口的防火墙是否关闭，否则也会连接不成功。



# 三、FFmpeg 推流

## 3.1 下载安装

FFmpeg 的安装（在window上）：

从官网这个页面[Builds - CODEX FFMPEG @ gyan.dev](https://www.gyan.dev/ffmpeg/builds/)：

![image-20230429192201129](https://gitee.com/yushen611/img/raw/master/image-20230429192201129.png)

找到下载的镜像网站https://www.gyan.dev/ffmpeg/builds/ffmpeg-git-github，下载圈出来的这个：

![image-20230429192254806](https://gitee.com/yushen611/img/raw/master/image-20230429192254806.png)

如果不想配置环境变量，就随便解压到一个文件夹，在哪个目录下运行exe文件即可完成推荐

<img src="https://gitee.com/yushen611/img/raw/master/image-20230429192532990.png" alt="image-20230429192532990" style="zoom:80%;" />

## 3.2 推流

**视频推流**

在cmd中运行如下命令进行视频推流：

```bash
ffmpeg -i D:\ywy\MYFILE\temp\test.mp4  -f flv rtmp://47.XXX.XXX.XXX/live/test1
```

D:\ywy\MYFILE\temp\test.mp4 是视频的路径，47.XXX.XXX.XXX是服务器的ip

**摄像头推流**

```bash
ffmpeg -f dshow -rtbufsize 100M -i video="XiaoMi USB 2.0 Webcam" -vcodec libx264 -preset:v ultrafast -tune:v zerolatency -f flv rtmp://47.XXX.XXX.XXX:1935/live/home
```





# 四、VCL拉流



vlc官网下载：[官方下载：VLC media player，最棒的开源播放器 - VideoLAN](https://www.videolan.org/vlc/index.zh_CN.html)

![image-20230429194610307](https://gitee.com/yushen611/img/raw/master/image-20230429194610307.png)

输入url:

<img src="https://gitee.com/yushen611/img/raw/master/image-20230429194658306.png" alt="image-20230429194658306" style="zoom:90%;" />

拉流成功

<img src="https://gitee.com/yushen611/img/raw/master/image-20230429194525293.png" alt="image-20230429194525293" style="zoom:67%;" />



