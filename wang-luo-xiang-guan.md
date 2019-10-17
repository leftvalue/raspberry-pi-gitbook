---
description: 咬牙花了56大洋买的外置网卡
---

# 网络相关

买了个外置网卡想倒腾点网络相关的东西,之前用过tp-link的WN722N,确实非常好用,windows以及linux都可以即插即用,但是有点贵,所以这次买了WN725N

{% embed url="https://www.tp-link.com/us/home-networking/usb-adapter/tl-wn725n/" %}

![&#x522B;&#x770B;&#x6211;&#x5C0F;](.gitbook/assets/edcc9b9e42ed9c35.jpg-q80.dpg.jpeg)

```bash
pi@raspberrypi:~ $ lsusb
Bus 001 Device 005: ID 0781:5567 SanDisk Corp. Cruzer Blade
Bus 001 Device 007: ID 0bda:1a2b Realtek Semiconductor Corp.
Bus 001 Device 003: ID 0424:ec00 Standard Microsystems Corp. SMSC9512/9514 Fast Ethernet Adapter
Bus 001 Device 002: ID 0424:9514 Standard Microsystems Corp. SMC9514 Hub
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
```

其中 Realtek Semiconductor Corp 便是刚插上的外置网卡

用不了,退了,安装不上驱动.

又买了一个 EDUP EP-N8508_GS_ 

![](.gitbook/assets/wx20190411-204741-2x.png)

倒是可以插上后直接识别设备了,但是不支持无线ap模式,又退了

多么痛的领悟~

又买了一个 EW-7811UN  ,插上即用,ifconfig 和 lsusb 都可以找到,默认无法开启 ap 模式,因为 hostpad 缺乏对应的网卡驱动

{% embed url="https://jenssegers.com/43/Realtek-RTL8188-based-access-point-on-Raspberry-Pi" caption="参照这个链接卸载\(如果已经安装过了hostpad\)之后重新手动编译hostpad" %}

## Network Manager 安装

```bash
sudo apt-get install network-manager
# 安装结束会自动重连网络,所以不要方,虽然连接断了,但是从路由器管理页能找到新的ip

sudo systemctl enable NetworkManager   
sudo service NetworkManager start
#[status|start|stop|reload|restart]
```

#### 注意,NetworkManager会自动在每次连接wifi的时候随机生成mac地址,但是我们在内网路由器通过mac地址绑定静态内网ip的情况下,并不希望mac地址随机更改,所以

修改 /etc/NetworkManager/NetworkManager.conf ,添加下面几行

```bash
[connection]
wifi.mac-address-randomization=1
 
[device]
wifi.scan-rand-mac-address=no
```



