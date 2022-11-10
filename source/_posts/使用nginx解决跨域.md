---
title: 使用nginx解决跨域
date: 2022-11-10 17:34:47
tags:
---

#### 若没有nginx，先安装nginx。安装解压后在nginx的目录下打开conf文件夹下的nginx.conf文件进行配置

##### 配置如下

```bash 
   server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            index  index.html index.htm;
            proxy_pass http://127.0.0.1:5500;
        }

        location /fetch {
            proxy_pass https://hotlist.imtt.qq.com/Fetch;
        }
   }
```

### 此时在项目中直接请求 /fetch 这个接口路径就行了， 请求127.0.0.1/fetch相当于https://hotlist.imtt.qq.com/Fetch


