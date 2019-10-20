
一个服务器通过多个端口访问多个项目
----------------------------------

今天我宿舍的好友工作中需要用到服务器来展示他的个人成果给老板来看，就向我借用服务器，我想也没问题，我的服务器也没什么访问压力，几个人或几十个放作品来供别人观看，我觉得也没什么压力的，但问题来了，我现在设置的方式只可以供自己放一个作品，那怎么做到可以放多个项目只用一个域名访问呢？
我百度了一下，发现还是蛮简单的。

1.虚拟机文件要做如下修改：

::

	<VirtualHost *:80>
	   ServerAdmin root@localhost
	   <Directory "/var/www/html">
	   Options FollowSymLINKS
	   AllowOverride All
	   Require all granted
	   </Directory>
	</VirtualHost>
	<VirtualHost *:82>
	   ServerAdmin root@localhost
	   DocumentRoot /var/www/weibo
	   <Directory "/var/www/html/weibo">
	   Options FollowSymLINKS
	   AllowOverride All
	   Require all granted
	   </Directory>
	</VirtualHost>
	
	只需要增加一个82端口即可，虚拟机配置的这些选项意思可以看下面：
	
::

	<Directory "/www">
        Options Indexes FollowSymLinks
        AllowOverride All
        Order allow,deny
        Allow from all        
        Require all granted   
	</Directory>
	
	Options是对该目录的一些选项, Indexes表示在没有index.html等文件的时候显示文件列表
	AllowOverride All 表示允许使用.htaccess文件重写URL
	Order allow,deny和Allow from all是对ip的访问配置
	Require all granted 意思是允许所有的请求
	
2.在httpd.conf文件中增加端口监听,并重启httpd，

::

	Listen82

同时确认最底部有
::

	IncludeOptional vhost-conf.d/*.conf
	
用来引用虚拟主机配置文件

3.在服务器开启82tcp服务端口

命令行操作
::

	semanage port -a -t http_port_t -p tcp 82

我是直接在官网的控制台改的安全组。
	
	.. figure:: /_static/images/2019102001.png
		:scale: 100
		:align: center

		
	好了，今天的配置就这么easy了！
																																						致——10-20


