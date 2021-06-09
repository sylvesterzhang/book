---
layout: post
title: nginx
tags: nginx
categories: backends
---

部署nginx、配置https和反向代理、负载均衡

**部署nginx**

`1`快速创建一个nginx容器

```shell
$ docker run -d -p80:80 nginx
```

`2`将配置文件复制出来到指定目录

```shell
$ docker cp nginx容器id:/etc/nginx/ /home/nginx/conf/
```

`3`暂停容器，并删除容器

```shell 
$ docker stop nginx容器id
```

```shell
$ docker container prune
```

`4`创建容器

```shell
$ docker run -d --restart=always -p 80:80 --name nginx-test-web \
  -v /home/nginx/www:/usr/share/nginx/html \
  -v /home/nginx/conf/:/etc/nginx/ \
  -v /home/nginx/logs:/var/log/nginx \
  nginx
```

`5`nginx特点

反向代理、负载均衡、动静分离

---

**配置https和反向代理**

`1`在/home/nginx/conf/目录下创建一个名为cert的文件夹，并将证书放入

`2`进入/home/nginx/conf/conf.d目录，创建一个以.conf结尾的文件，文件内容如下

```
server {
    listen 443;
    server_name abcde.cool;
    ssl on;
    root html;
    index index.html index.htm;
    
    #配置证书
    ssl_certificate   cert/4236706_abcde.cool.pem;
    ssl_certificate_key  cert/4236706_abcde.cool.key;
    
    ssl_session_timeout 5m;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;

	#配置反向代理，注意地址不可为127.0.0.1
    location ^~/subject {#当路径为/subject时，服务器地址指向http://117.50.141.71:8081
        proxy_pass http://117.50.141.71:8081;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto https;
    }

    location ^~/mall {
        proxy_pass http://117.50.141.71:8088;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto https;
    }


    location / {
        root html;
        index index.html index.htm;
    }

    
}
```

---

**负载均衡**

```
server{
	listen       80;
    server_name  localhost;
    proxy_read_timeout 600;
    charset utf-8;
    
    location /hello{
    	proxy_pass http://helloservers;#指向服务器组
    }    	
}
upstream helloservers{#服务器组	
    server 192.168.17.1:8080 weight=1;
    server 192.168.17.1:8081 weight=1;
    server 192.168.17.1:8082 weight=1;
}
```

 目前Nginx的upstream模块支持6种方式的负载均衡策略（算法）：轮询（默认方式）、weight（权重方式）、ip_hash（依据ip分配方式）、least_conn（最少连接方式）、fair（第三方提供的响应时间方式）、url_hash（第三方通过的依据URL分配方式） 