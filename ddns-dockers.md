# DDNS dockers

### No-ip

{% embed url="https://hub.docker.com/r/hypriot/rpi-noip" %}

* [https://hub.docker.com/r/hypriot/rpi-noip](https://hub.docker.com/r/hypriot/rpi-noip)

```bash
sudo docker run -ti -v "noip:/usr/local/etc/" hypriot/rpi-noip noip2 -C
# This step will ask for your noip credentials and generate the necessary configuration for your container to run
sudo usdocker run -d -v "noip:/usr/local/etc/" --restart=always hypriot/rpi-noip
```

