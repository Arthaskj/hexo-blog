title: Nginx配置
category: 技能
tags: nginx
date: <文章日期> [YYYY-MM-DD]

---

Nginx配置


<!--more-->

# Nginx配置

> nginx.conf

```nginx
#user www-data;
worker_processes 1;
#pid /run/nginx.pid;
#include /etc/nginx/modules-enabled/*.conf;

events {
	worker_connections 1024;
	# multi_accept on;
}

http {

	##
	# Basic Settings
	##

	sendfile on;
	tcp_nopush on;
	tcp_nodelay on;
	keepalive_timeout 65;
	types_hash_max_size 2048;
	# server_tokens off;

	# server_names_hash_bucket_size 64;
	# server_name_in_redirect off;

	include /etc/nginx/mime.types;
	default_type application/octet-stream;

	##
	# SSL Settings
	##

	ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3; # Dropping SSLv3, ref: POODLE
	ssl_prefer_server_ciphers on;

	##
	# Logging Settings
	##

	access_log /var/log/nginx/access.log;
	error_log /var/log/nginx/error.log;

	##
	# Gzip Settings
	##

	gzip on;

	# gzip_vary on;
	# gzip_proxied any;
	# gzip_comp_level 6;
	# gzip_buffers 16 8k;
	# gzip_http_version 1.1;
	# gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

	##
	# Virtual Host Configs
	##

    # 可以指定一个目录下的所有.conf文件，这样一个项目一个配置文件，比全部写在nginx.conf下要整洁很多
	include /mnt/h/nginx/config/*.conf;
	#include /etc/nginx/sites-enabled/*;
}
```



> trade.conf

```nginx
server {
        listen       8088;
        server_name  localhost;
		# 设置变量方便统一处理
		set $api_proxy http://192.168.0.149:8003;
		set $tradeapi_proxy http://192.168.0.190:8090;
		set $quota_proxy http://192.168.0.191:8090;
      

		#行情websocket代理
		location ~ ^(/tradeapi/abc/123) {
        	# 对url进行重写
			rewrite /tradeapi/abc/123 /tradeapi/abc/456 break;
			proxy_pass $quota_proxy;
			proxy_connect_timeout 10s;
			proxy_http_version 1.1;
			proxy_set_header Host $host;
			proxy_set_header X-Real-IP $remote_addr;
			proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
			proxy_read_timeout 120s;
        
        	# 支持websocket转发
			proxy_set_header Upgrade $http_upgrade;
			proxy_set_header Connection "Upgrade";
		}
		
		 # 显示行情列表
        location /tradeapi/api/url {
            proxy_pass $quota_proxy;
			proxy_connect_timeout 10s;
			proxy_http_version 1.1;
			proxy_set_header Host $host;
			proxy_set_header X-Real-IP $remote_addr;
			proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
			proxy_read_timeout 120s;
        }

		#接口代理(包含交易系统websocket)
		location /tradeapi {
			proxy_pass $tradeapi_proxy;
			proxy_connect_timeout 10s;
			proxy_http_version 1.1;
			proxy_set_header Host $host;
			proxy_set_header X-Real-IP $remote_addr;
			proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
			proxy_read_timeout 120s;
        
        	# 支持websocket转发
			proxy_set_header Upgrade $http_upgrade;
			proxy_set_header Connection "Upgrade";
		}

        location / {
            proxy_pass $api_proxy;
        }

    }
```



常用指令

```shell
# ubuntu
service nginx start
service nginx stop
service nginx restart
# 查看相关nginx文件的目录，V大写
nginx -V 

# centos
systemctl start nginx.service
systemctl stop nginx.service
systemctl restart nginx.service

# macOS(管理员sudo模式)
brew services start nginx
brew services stop nginx
brew services restart nginx

# windows
./nginx.exe
./nginx.exe -s stop
./nginx.exe -s reload
```

