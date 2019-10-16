---
description: Samba
---

# raspberryPi 文件同步/增删

**Samba 安装**

```bash
sudo apt-get install samba
sudo apt-get install samba-common-bin
sudo vi /etc/samba/smb.conf

# read only = yes” 改为 “read only = no
sudo smbpasswd -a pi
sudo smbd restart
```

```text
\\192.168.6.123\pi
```

## filebrowser

{% embed url="https://filebrowser.xyz/installation" caption="File Browser" %}

默认情况下如果使用 nginx 上传/下载,会受 nginx 的最大请求大小限制,建议修改nginx的配置文件并重启 nginx

```bash
client_max_body_size 20M;
# 可以选择在http{ }中设置：client_max_body_size 20m;
# 也可以选择在server{ }中设置：client_max_body_size 20m;
# 还可以选择在location{ }中设置：client_max_body_size 20m;
# 三者有区别
# 设置到http{}内，控制全局nginx所有请求报文大小
# 设置到server{}内，控制该server的所有请求报文大小
# 设置到location{}内，控制满足该路由规则的请求报文大小 
```





​

