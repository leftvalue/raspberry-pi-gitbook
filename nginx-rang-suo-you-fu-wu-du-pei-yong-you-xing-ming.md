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

{% code-tabs %}
{% code-tabs-item title="/etc/nginx/conf.d/global.conf" %}
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
{% endcode-tabs-item %}
{% endcode-tabs %}

reload 配置文件 

```bash
sudo /etc/init.d/nginx reload
```

## Webmin

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

{% code-tabs %}
{% code-tabs-item title="/etc/nginx/conf.d/global.conf" %}
```text
location /webmin/{
    proxy_pass http://127.0.0.1:10000/; 
    proxy_redirect http://$host:10000/ /webmin/;
    # Also fixes initial redirect after login 
    proxy_set_header Host $host;
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

reload 配置文件 

```bash
sudo /etc/init.d/nginx reload
```



## Nginx 个人配置文件 \( 持续更新中 ... \)

{% code-tabs %}
{% code-tabs-item title="/etc/nginx/conf.d/global.conf" %}
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
    		proxy_pass http://127.0.0.1:10000/;
    		proxy_redirect http://$host:10000/ /webmin/;
    		# Also fixes initial redirect after login
    		proxy_set_header Host $host;
	}

}
```
{% endcode-tabs-item %}
{% endcode-tabs %}





