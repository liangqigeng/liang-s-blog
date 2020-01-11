
ubuntu的nginx配置
----------------------------------

今天尝试在虚拟机ubuntu上配置nginx，目前我主要用的Linux的系统就两种，CentOS和ubuntu，CentOS感觉在配置服务器上是相当简单，ubuntu感觉就用得没那么习惯，有很多指令都要重新记忆一下。也为我之后学习docker打好基础吧。
	Start Apache 2 Server /启动apache服务
$ sudo /etc/init.d/apache2 start


Restart Apache 2 Server /重启apache服务
$ sudo /etc/init.d/apache2 restart


Stop Apache 2 Server /停止apache服务
$ sudo /etc/init.d/apache2 stop
这是控制apache服务的指令
sudo apt-get --purge remove nginx
2、移除全部不使用的软件包
sudo apt-get autoremove
3、罗列出与nginx相关的软件并删除
dpkg --get-selections|grep nginx
sudo apt-get --purge remove nginx
sudo apt-get --purge remove nginx-common
sudo apt-get --purge remove nginx-core
4、查看nginx正在运行的进程，如果有就kill掉
ps -ef |grep nginx
sudo kill -9 XXX

三、配置Nginx
最新版本nginx配置是由4个文件构成：

conf.d：用户自己定义的conf配置文件
sites-available：系统默认设置的配置文件
sites-enabled：由sites-available中的配置文件转换生成
nginx.conf：汇总以上三个配置文件的内容，同时配置我们所需要的参数
在部署需要的web服务时，我们可以拷贝sites-enabled中的default文件到conf.d并且修改名字为**.conf,然后进行配置
server {
    #服务启动时监听的端口
    listen 80 default_server;
    listen [::]:80 default_server;
    #服务启动时文件加载的路径
    root /var/www/html/wordpress;
    #默认加载的第一个文件
    index index.php index.html index.htm index.nginx-debian.html;
    #页面访问域名，如果没有域名也可以填写_
    server_name www.xiexianbo.xin;

    location / {
        #页面加载失败后所跳转的页面
        try_files $uri $uri/ =404;
    }
    
      
    #以下配置只服务于php
    # 将PHP脚本传递给在127.0.0.1:9000上监听的FastCGI服务器
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        # With php7.0-cgi alone:
        #fastcgi_pass 127.0.0.1:9000;
        # With php7.0-fpm:
        fastcgi_pass unix:/run/php/php7.0-fpm.sock;
    }

    # 如果Apache的文档为root，则拒绝访问.htaccess文件
    location ~ /\.ht {
        deny all;
    }
}

注意事项：

apache的端口也是80，所以我们可以选择关闭apache或者，在这里更换端口，例如81，82等，但是我们需要吧这个端口开放出来
React、Vue等由于是单页面应用，所以我们在刷新的会遇到资源加载不到的错误，这时我们需要把页面重定向到index.html

try_files $uri /index.html;
每次配置完成后，都需要重启nginx。