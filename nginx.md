# nginx

## 配置代理

### 代理本地的一个静态文件夹路径加 / 与不加 /

- 加 /

```nginx.conf
server_name localhost
location /data/ {
proxy_pass http://127.0.0.1/;
}
```

访问 http://localhost/data/index.html 会转发到 http://127.0.0.1/index.html

- 不加 /

```nginx.conf
server_name shaochenfeng.com
    location /data/ {
    proxy_pass http://127.0.0.1;
}
```

访问 http://localhost/data/index.html 会转发到 http://127.0.0.1/data/index.html



### 代理本地的接口到另外一个地址

```
server{
  listen 80;
  server_name  localhost;
  index  index.html index.html index.htm;

  location / {
    proxy_pass  http://127.0.0.1:8080; # 转发规则
    proxy_set_header Host $proxy_host; # 修改转发请求头，让8080端口的应用可以受到真实的请求
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }
}
```

这样访问 http://localhost 时就会转发到本地的 8080 端口











