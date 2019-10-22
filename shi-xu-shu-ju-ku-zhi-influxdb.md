# 时序数据库之 influxDb & 它的好搭档 Grafana

## InfluxDb

{% embed url="https://github.com/hypriot/rpi-influxdb" %}

```bash
sudo docker run -d --volume=/home/pi/influxdb/data:/data --name influxdb -p 8086:8086 -e ADMIN_USER="root" -e INFLUXDB_INIT_PWD="1234567890" hypriot/rpi-influxdb
```

## Grafana

{% hint style="info" %}
新版的 grafana 官方已经支持 armf,所以不需要用其他的第三方docker
{% endhint %}

```bash
sudo docker run -d --name=grafana -p 4000:3000 --restart=always grafana/grafana-arm32v7-linux:dev-musl
```

默认用户名 admin 密码 admin ,第一次登录后会提示进行修改

## 组网

为了让docker 里的 influxdb 和 grafana 能够互相通讯,我们需要对他们进行组网,docker 1.9以上推荐使用 Docker Networking

```bash
sudo docker network create app
sudo docker network connect --alias influxdb app influxdb
sudo docker network connect --alias grafana app grafana
```

