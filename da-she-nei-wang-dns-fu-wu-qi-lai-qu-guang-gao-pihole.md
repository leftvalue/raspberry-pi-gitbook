---
description: '@Deprecated pi-hole 有稳定性问题,dns 服务器经常失去相应,决定弃了投 Adguard-Home,详见下篇文章'
---

# 搭设内网 dns 服务器来去广告  Pi-hole

{% hint style="info" %}
部分路由器不支持端口回流 \(Hairpin NAT/ NAT loopback / SNAT \) ,通过内网搭建 dns 服务器并显示指定某公网域名到内网 ip 可以变相解决此问题 
{% endhint %}

{% embed url="https://www.hwchiu.com/nat-loopback.html" caption="端口回流介绍" %}

## Pi-hole

{% embed url="https://pi-hole.net/" caption="搭建等信息见官网" %}

### pihole 存在无法卸载完全的问题,建议安装在 docker 里方便维护

```bash
docker run -d \
    --name pihole \
    -p 53:53/tcp -p 53:53/udp \
    -p 80:80 \
    -p 443:443 \
    -e TZ="Asia/Chongqing" \
    -v "$(pwd)/etc-pihole/:/etc/pihole/" \
    -v "$(pwd)/etc-dnsmasq.d/:/etc/dnsmasq.d/" \
    --dns=127.0.0.1 --dns=1.1.1.1 \
    --restart=unless-stopped \
    pihole/pihole:latest

# 这样安装的 pihole 情况下,
docker exec -it pihole_container_name pihole -a -p # 这样来改密码
#其中 pihole_container 为前面指定的 pihole container 的名称(pihole)
# 后面的pihole 命令统一替换成
docker exec -it pihole_container_name pihole
```

```bash
# 初次安装成功后会告诉你默认密码,为了防止记不住,建议给改了
sudo pihole -a -p yournewpassword
```

安装成功后,默认web-ui 监听本地80 端口,  http://yourraspberrypi/admin

```bash
# 查看当前机器的 dns 
cat /etc/resolv.conf

# 下面是我的树莓派安装 pihole 成功以后的执行上面语句的结果
## Dynamic resolv.conf(5) file for glibc resolver(3) generated by resolvconf(8)
##     DO NOT EDIT THIS FILE BY HAND -- YOUR CHANGES WILL BE OVERWRITTEN
## nameserver 127.0.0.1 
```

### 添加自定义 DNS 规则

```bash
# 添加解析记录
pihole -a -r outerdomain.com 10.0.0.1

# 删除上面添加的解析记录(其实就是给一个空的解析记录)
pihole -a -r outerdomain.com

# 删除所有解析记录
pihole -a -r
```

{% embed url="https://github.com/neoFelhz/neohosts" caption="一个比较好用的黑名单" %}

```text
https://github.com/neoFelhz/neohosts
```

{% embed url="https://github.com/Diumo/ADhosts/blob/master/README.md" %}

```text
# 几个黑名单合集
https://github.com/Diumo/ADhosts/blob/master/README.md
```





