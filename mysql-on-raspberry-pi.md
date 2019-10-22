# Mysql on Raspberry-pi

{% hint style="info" %}
一些数据需要在 raspberrypi 进行存储,mysql 无疑是一个比较合适的选择,笔者选择在 docker 中安装 mysql,下面围绕这个单机 mysql 展开
{% endhint %}

## 初始化安装

{% embed url="https://hub.docker.com/r/hypriot/rpi-mysql/" %}

```bash
sudo docker pull hypriot/rpi-mysql
sudo docker run -d -p 23306:3306 --name mysql -e MYSQL_ROOT_PASSWORD=1234567890 -v /home/pi/mysql/data:/var/lib/mysql hypriot/rpi-mysql:latest使用
```

#### mysql 命令行

```bash
sudo docker exec -it mysql /bin/bash
# 进入 MySQL container 的 terminal
mysql -u root -p
```

