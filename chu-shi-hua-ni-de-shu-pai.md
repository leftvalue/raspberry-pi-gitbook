---
description: 得到一个干净的系统和英雄的起源
---

# 初始化你的树莓派

## 准备工作

* [ ] 树莓派 3代 b + 
* [ ] 内存卡
* [ ] 读卡器
* [ ] [https://www.raspberrypi.org/downloads/raspbian/](https://www.raspberrypi.org/downloads/raspbian/) 下载官方镜像 zip
* [ ] [https://www.balena.io/etcher/](https://www.balena.io/etcher/) 下载 Etcher 系统刻录工具

{% hint style="info" %}
初学者建议使用 "Raspbian Buster with desktop and recommended software" 版本镜像,可以省去不少麻烦,自带桌面

对linux 有基础者建议使用 "Raspbian Buster Lite-Minimal image based on Debian Buster"镜像,更小并且更稳定,日常系统占用更低
{% endhint %}

## 刻录系统

![](.gitbook/assets/steps-8006dca57323756b1b84fb9408742409.gif)

### 启动前配置

```bash
cat /Volumes/boot/
touch ssh # 为了允许树莓派 ssh 登录
```

在树莓派根目录创建 wifi 配置文件

> wpa\_supplicant.conf

更多详细用法参照 [https://www.raspberrypi.org/documentation/configuration/wireless/wireless-cli.md](https://www.raspberrypi.org/documentation/configuration/wireless/wireless-cli.md)

{% tabs %}
{% tab title="wpa\_supplicant.conf" %}
```bash
country=CN
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1

network={
    ssid="ziroom-301"
    psk="18810205578"
    priority=5
    id_str="home"
}

network={
    ssid="kristendi"
    psk="dk010203040506"
    priority=4
    id_str="myAndroid"
}
```
{% endtab %}
{% endtabs %}

可以把 tf 卡插到树莓派上并接通电源了

## 启动树莓派后🐵

### 如何获取树莓派的内网ip?

如果有路由器管理密码

![&#x5404;&#x54C1;&#x724C;&#x8DEF;&#x7531;&#x5668;&#x7BA1;&#x7406;&#x9875;&#x9762;&#x53EF;&#x80FD;&#x7565;&#x6709;&#x4E0D;&#x540C;](.gitbook/assets/1%20%281%29.png)

如果没有路由器管理密码\(比如在校园网或公司网内\)

```bash
# more about arp : Address Resolution Protocol
# https://www.computerhope.com/unix/arp.htm
arp -a|grep raspberrypi.lan
# raspberrypi.lan (192.168.199.200) at b8:27:eb:63:4e:ce on en0 ifscope [ethernet]
```

假设获取了 ip 为 192.168.199.200

```bash
ssh pi@192.168.199.200
# 默认密码 : raspberry
```

![&#x9ED8;&#x8BA4;&#x767B;&#x5F55;&#x6B22;&#x8FCE;&#x9875;](.gitbook/assets/carbon-2.svg)

```bash
# 改密码,根据提示操作
passwd
```

### 关于用户账户的碎碎念

* 默认账户为 pi ,  sudo su 可切换管理员账户\(不需要另行输入密码\)
* sudo passwd root 可用于添加 root 账户

> If you want to login as root using SSH or WinSCP you need to edit the config of SSHD, do this:
>
> * Login, and edit this file: `sudo vi /etc/ssh/sshd_config`
> * Find this line: `PermitRootLogin without-password`
> * Edit: `PermitRootLogin yes`
> * Close and save file
> * `reboot` or restart sshd service using: `/etc/init.d/ssh restart`
> * Set a root password if there isn't one already: `sudo passwd root`
>
> Now you can login as `root`, but I recommend you using strong password or ssh-keys

{% embed url="https://raspberrypi.stackexchange.com/questions/48056/how-to-login-as-root-remotely" %}



