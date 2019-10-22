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
sudo docker run -d --name=grafana -p 3000:3000 --restart=always grafana/grafana-arm32v7-linux:dev-musl
```

