---
description: 如题
---

# Nginx : 让所有服务都配拥有姓名

平时记忆一堆端口用来访问 Raspberry Pi 上的各种服务不是很方便呢怎么办? 一些免费的端口转发只支持80端口怎么办?有了Nginx,一切都不是问题

## 安装

{% embed url="https://www.raspberrypi.org/documentation/remote-access/web-server/nginx.md" %}

```bash
sudo apt-get install nginx
sudo /etc/init.d/nginx start
# 配置文件路径
# /etc/nginx
# 如果要配置端口转发,记得注释掉 include /etc/nginx/sites-enabled/*;
# 检查配置文件
sudo /etc/init.d/nginx reload
# 重载配置文件
sudo /usr/sbin/nginx -t
```

{% hint style="info" %}
因为 webmin Grafana 等项目中有一部分地址是绝对路径,所以不能直接简单的配置proxy\_pass来反向代理,我们逐个击破
{% endhint %}

## Grafana

{% embed url="https://serverfault.com/questions/684709/how-to-proxy-grafana-with-nginx" %}

### grafana 配置

```bash
sudo vi /etc/grafana/grafana.ini
# 添加
root_url = %(protocol)s://%(domain)s:%(http_port)s/grafana

# 保存后
sudo /bin/systemctl restart grafana-server
```

### nginx 对应配置

```bash
sudo vi /etc/nginx/conf.d/global.conf
```

{% tabs %}
{% tab title="/etc/nginx/conf.d/global.conf" %}
```text
server {
    listen 80;
    server_name localhost; 
    location /grafana {
        proxy_pass http://localhost:3000;
        rewrite  ^/grafana/(.*)  /$1 break;
        proxy_set_header   Host $host;
    }
}
```
{% endtab %}
{% endtabs %}

reload 配置文件 

```bash
sudo /etc/init.d/nginx reload
```

## Webmin

{% embed url="https://serverfault.com/questions/443482/proxying-webmin-with-nginx" %}

### webmin 配置

```bash
sudo vi /etc/webmin/config
# 修改
webprefix=/webmin
# 添加
webprefixnoredir=1

# 保存后
sudo /etc/init.d/webmin restart
```

### nginx 配置

添加到前述的 `/etc/nginx/conf.d/global.conf` 

{% tabs %}
{% tab title="/etc/nginx/conf.d/global.conf" %}
```text
location /webmin/{
    # 为了能够upload文件,解决 413 Request Entity Too Large
    client_max_body_size 50m;
    proxy_pass http://127.0.0.1:10000/; 
    proxy_redirect http://$host:10000/ /webmin/;
    # Also fixes initial redirect after login 
    proxy_set_header Host $host;
}
```
{% endtab %}
{% endtabs %}

reload 配置文件 

```bash
sudo /etc/init.d/nginx reload
```



## Nginx 个人配置文件 \( 持续更新中 ... \)

{% tabs %}
{% tab title="/etc/nginx/conf.d/global.conf" %}
```text
server {
	listen       80;
	server_name  localhost;

	location /grafana {
		proxy_pass http://localhost:3000;
     		rewrite  ^/grafana/(.*)  /$1 break;
     		proxy_set_header Host $host;
	}

	location /webmin/{
			# 为了能够upload文件,解决 413 Request Entity Too Large
    		client_max_body_size 50m;
        	proxy_pass http://127.0.0.1:10000/;
    		proxy_redirect http://$host:10000/ /webmin/;
    		# Also fixes initial redirect after login
    		proxy_set_header Host $host;
	}

}
```
{% endtab %}
{% endtabs %}

## 面向外网进行端口转发到 NGINX 服务

{% tabs %}
{% tab title="/etc/nginx/nginx.conf" %}
```text
user www-data;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;
events {
    worker_connections 768;
}
http {
    keepalive_timeout 65;
    server {
        listen 8888;
# 外网上传下载文件,突破默认大小限制
        client_max_body_size 5000m;
        server_name *.yourdomain.com;
        if ($http_host ~* "^(.*?)\.yourdomain\.com:8888$") {
#正则表达式
            set $domain $1;
        }
        location / {
            if ($domain ~* "^\d*$") {
                proxy_pass http://127.0.0.1:$domain;
            }
			tcp_nodelay on;
			proxy_set_header Host            $host;
			proxy_set_header X-Real-IP       $remote_addr;
			proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }
    }
    server {
        listen 8080;
        server_name localhost;
        location / {
#root   html;
#index  index.html index.htm;
            add_header Content-Type 'text/plain; charset=utf-8';
            return 200 "你今天真好看呀 :)";
        }
    }
    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;
    gzip on;
}

```
{% endtab %}
{% endtabs %}

如果配上面的接口,在路由器\(假设IP 为 router\_ip \)配置了 8888  端口转发到 搭了上面 nginx 配置的树莓派 ip\( raspberry\_ip \)的8888 

```text
router_ip:8888 -> raspberry_ip:8888
```

同时满足我们设置了

```text
*.domain.com -> router_ip
```

这样的一条 A 记录 ddns

那么, 树莓派上的某端口\(如9000\)可用如下方式访问

* raspberry\_ip:9000
* 9000.domain.com:8888

其中 _**9000.domain.com:8888**_ 便是我们想要强调的,通过一个 nginx 来使分配了端口转发的子域名拥有二次端口转发的灵活性,同时不需要单独配置新的端口转发规则.

{% hint style="info" %}
注意,这样相当于把树莓派的所有端口都对外网暴露了,必须严格控制访问权限,否则有严重安全风险
{% endhint %}

### NGINX 转发 SSH

上面我们说到,通过 nginx 可实现只用一个端口实现多个端口的对外转发,但是有一个问题在于上面我们的配置不能用于 ssh 连接的转发,参阅下面的文档

{% embed url="http://nginx.org/en/docs/stream/ngx\_stream\_core\_module.html" %}

笔者发现树莓派上通过 apt-get 安装的 nginx 其实已经带了 stream 模块了,不需要重新编译

```text
stream{
    upstream machine1{
        server 127.0.0.1:22;       #内网服务器102，端口为sshd默认的22
    }

    server {
        listen 2333;
        proxy_connect_timeout 1h;
        proxy_timeout 1h;
        proxy_pass machine1;            #转向内网服务器101
    }
}
```

stream 区块放在与 http 区块平级的位置即可.

### 为你的 vim 添加 nginx 配置文件高亮

相信前面对着窗口改配置文件确实够头疼,而且颜色不易区分,

{% embed url="https://gist.github.com/ralavay/c4c7750795ccfd72c2db" %}

参照上面给出的代码可给你的 vim 插上 nginx 彩色的翅膀\(逃

```bash
# 这里和上面是一样的,防止上面链接内容加载不出来所以在这里备份一下
mkdir -p ~/.vim/syntax/
wget http://www.vim.org/scripts/download_script.php?src_id=19394 -O ~/.vim/syntax/nginx.vim

# Set location of Nginx config file
cat > ~/.vim/filetype.vim <<EOF
au BufRead,BufNewFile /etc/nginx/*,/etc/nginx/conf.d/*,/usr/local/nginx/conf/* if &ft == '' | setfiletype nginx | endif
EOF
```





