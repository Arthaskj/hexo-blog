### 模块化配置多个Nginx站点

#### 主配置文件

```nginx
# nginx.conf

#user  nobody;
#user root owner;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
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
  
    # 设置子配置文件地址
    include /Users/kejun/Nginx/configs/*.conf;

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
    include servers/*;
}

```



#### 子配置文件

```nginx
#/Users/kejun/Nginx/configs/child.conf

server {
        listen       8082;
        server_name  localhost;

        #接口代理
        location ~ ^(/api|/Home|/AuthConfig.axd|/signalr) {
            proxy_pass http://192.168.3.223:8099;
            proxy_connect_timeout 10s;
            proxy_http_version 1.1;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_read_timeout 120s;
        }

          #静态资源
        location ~ ^(/WebSource|/html|/assets) {
            root /Users/kejun/workspace/h6_f1;
            add_header Last-Modified $date_gmt;
            add_header Cache-Control "public";
            expires 5m;
        }

          #老项目入口文件
        #切记，作为边缘节点的index.html不需要设置缓存
        location / {
            root /Users/kejun/workspace/h6_f1/html;
            index Index.html Index.html;
            #try_files $uri $uri/ /index.html =404;
        }


    }
```

