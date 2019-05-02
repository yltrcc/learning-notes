# 配置文件备份

```Bash
nginx.conf

        ## service conf
        server {
                listen 80;
                server_name localhost;
                rewrite ^(.*)$ https://$host$1; #将所有HTTP请求通过rewrite指令重定向到HTTPS。
        }
        #以下属性中，以ssl开头的属性表示与证书配置有关。
        server {
                listen 443 ssl;
                #配置HTTPS的默认访问端口为443。
                #如果未在此处配置HTTPS的默认访问端口，可能会造成Nginx无法启动。
                #如果您使用Nginx 1.15.0及以上版本，请使用listen 443 ssl代替listen 443和ssl on。
                server_name blog.ylcoder.top; #需要将yourdomain.com替换成证书绑定的域名。
                root html;
                index index.html index.htm;
                ssl_certificate cert/6674254_blog.ylcoder.top.pem;  #需要将cert-file-name.pem替换成已上传的证书文件的名称。
                ssl_certificate_key cert/6674254_blog.ylcoder.top.key; #需要将cert-file-name.key替换成已上传的证书私钥文件的名称。
                ssl_session_timeout 5m;
                ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
                #表示使用的加密套件的类型。
                ssl_protocols TLSv1.1 TLSv1.2 TLSv1.3; #表示使用的TLS协议的类型。
                ssl_prefer_server_ciphers on;
                location / {
                        proxy_pass http://localhost:8080;
                        proxy_redirect off;
                        proxy_set_header Host $host;
                        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                        proxy_set_header X-Real-IP $remote_addr;
                }
        }
        server {
                listen 443 ssl;
                server_name ylcoder.top; #需要将yourdomain.com替换成证书绑定的域名。
                return 301 https://www.ylcoder.top;
        }


```