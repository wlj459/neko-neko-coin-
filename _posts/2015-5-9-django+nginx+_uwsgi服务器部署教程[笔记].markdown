---
layout: post
title:  "Django+Nginx+uWSGI服务器部署教程[笔记]" 
date:   2015-5-9
---
因为要给前端大爷搭环境测试，就得去部署。之前研究过一点点，懒得弄就放弃了。终于在前端大爷的淫威之下，速成部署。唔，整理一下流程和遇到的问题，防止忘记了。
在网上找到了一个蛮清楚的[教程](http://django-china.cn/topic/101/)分上下两章，介绍的很清楚。
#####第一步配置Django
编写django_wsgi.py文件，将其放在与文件manage.py同一个目录下

	#-*- coding:utf-8-*-
	import os
	import sys

	reload(sys)
	sys.setdefaultencoding('utf8')

	os.environ.setdefault("DJANGO_SETTINGS_MODULE", "MultiPlayer.settings")

	from django.core.handlers.wsgi import WSGIHandler
	application = WSGIHandler()

执行下面代码就可以搭建起一个简易的web服务器
uwsgi --http :8000 --chdir your app file path --module django_wsgi

#####第二步配置uWSGI

新建一个XML文件，将其放在与文件manage.py同一个目录下

	<uwsgi>
	    <socket>:8077</socket>
	    <chdir>your app file path</chdir>
	    <module>django_wsgi</module>
	    <processes>4</processes> <!-- 进程数 --> 
	    <daemonize>uwsgi.log</daemonize>
	</uwsgi>
#####第三步配置Nginx
为nginx.conf添加以下配置

	http{
		server {

			listen   80;
			server_name www.you.com;
			access_log /home/work/var/test/logs/access.log;
			error_log /home/work/var/test/logs/error.log;

			#charset koi8-r;

			#access_log  logs/host.access.log  main;

			location / {
			 include        uwsgi_params;
			 uwsgi_pass     127.0.0.1:8077;
			}

			#error_page  404              /404.html;

			# redirect server error pages to the static page /50x.html
			#
			error_page   500 502 503 504  /50x.html;
			location = /50x.html {
			    root   html;
			}

			location /static/ {
			    alias  /home/work/src/sites/testdjango1/testdjango/collectedstatic/;
			    index  index.html index.htm;
			}

			location /media/ {
			    alias  /home/work/src/sites/testdjango1/testdjango/public/media/;
			}
		    }
		}

‘nginx -s  reload’重启Nginx
'uwsgi -x XXX.xml'(xxx是你所创建的XML文件名称)启动uWSGI

######关于如何获得nginx.conf的路径
使用'nginx -t'编译nginx.conf文件，会输出它的路径

#####出现的问题
测试的这个项目牵扯到静态文件的访问，但是每次访问都是403.就算是文件权限全都777了还是拒绝我。看日志告诉我是权限问题。但是尼码我都改成777了啊摔。最后谷歌后发现，是nginx.conf的锅。
在文件中有一个user,千千万万改成root！！！！
唔，后来问了一下，是因为在root环境下的原因。需要把文件的所属人换成user的那个，直接换root太暴力啦~

