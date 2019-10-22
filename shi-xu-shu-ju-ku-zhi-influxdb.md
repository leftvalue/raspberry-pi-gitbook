# 时序数据库之 influxDb

{% embed url="https://github.com/hypriot/rpi-influxdb" %}

```bash
sudo docker run -d --volume=/home/pi/influxdb/data:/data --name influxdb -p 8086:8086 -e ADMIN_USER="root" -e INFLUXDB_INIT_PWD="1234567890" hypriot/rpi-influxdb
```

