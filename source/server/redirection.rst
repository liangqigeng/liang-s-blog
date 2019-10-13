
重定向
---------------------

今天配置服务器遇到一个问题搞了老半天，还有我对linux的apache的配置文件路径一直记不住，顺便也一并记录下来。

首先，我的网站liangqigeng.top是受保护，但www.liangqigeng.top指向是不受保护的，原因是我在ssl里面没有包含这个域名。我想重定向www到我的一级域名，然后很多问题拖了我一个多小时。首先是apache的配置文件，是在 /etc/httpd/conf/httpd.conf，虚拟机配置文件位置/etc/httpd/conf.d/vhost.conf

1.vhost.conf配置内容如下：
::

	<VirtualHost *:443>
		DocumentRoot "/var/www/html/weibo"
		ServerName localhost
	</VirtualHost>
	<VirtualHost *:443>
		DocumentRoot "/var/www/html/weibo/"
		ServerName localhost
	<Directory "/var/www/html/weibo/">
		Options Indexes FollowSymLinks
		AllowOverride All
		Order allow,deny
		Allow from all
	</Directory>
		ErrorLog logs/www_online_com-error_log
		 CustomLog logs/www_online_com-access_log common
	</VirtualHost>
		
因为我指向的https，所以端口都是443，如果不写443，访问速度会很慢。

2.httpd/conf文件需要作如下修改：
::
	
	<Directory /var/www/html >
		Options FollowSymLinks
		AllowOverride None
		……
	</Directory>
				
AllowOverride 后面的None改为All即可。但要注意一定是这个<Directory /var/www/html >开头的才有效。我就是手动改/var/www变成/var/www/html而其实后面还有，所以一直没效果，血泪的教训。
	
.htaccess的编写规则如下

实例 ：
::	

	RewriteEngine on //打开rewirte功能
	RewriteCond %{HTTP_HOST} !^www.smilejay.com [NC] //声明Client请求的主机中前缀不是www.smilejay.com，其中 [NC] 的意思是忽略大小写
	RewriteCond %{HTTP_HOST} !^173.192.169.119 [NC] //声明Client请求的主机中前缀不是173.192.169.119，其中 [NC] 的意思是忽略大小写
	RewriteCond %{HTTP_HOST} !^$ //声明Client请求的主机中前缀不为空
	RewriteRule ^(.*) http://www.smilejay.com/ [L] //含义是如果Client请求的主机中的前缀符合上述条件，则直接进行跳转到http
	
3.vim /var/www/html/.htaccess添加以下规则实现www跳转一级域名：

方案一：
::
	
	RewriteEngine on 
	RewriteCond  %{HTTP_HOST} ^www.liangqigeng.top [NC]
	RewriteRule  ^(.*) https://liangqigeng.top [L]
	
方案二：
::
	
	RewriteEngine On
	RewriteBase /
	RewriteCond %{HTTP_HOST} ^www\.(.*)$ [NC]
	RewriteRule ^(.*)$ https://%1 [R=301,L]
		
（刚发现火狐旧版54.0都不支持 谷歌可以）
	
systemctl restart httpd 重启apache即可生效
	
如果还是不行，可以检查/etc/httpd/conf.modules.d/00-base.conf中的LoadModule rewrite_module modules/mod_rewrite.so模块前面是否有#号，默认是开启的了。如果没有开启的话去掉#即可。好了，今天的总结就这些了。


	



																																																												致——10-13

