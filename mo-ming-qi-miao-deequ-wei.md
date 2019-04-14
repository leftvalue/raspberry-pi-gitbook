---
description: 杂七杂八的便捷脚本汇总
---

# 莫名其妙的恶趣味

## 开机音乐

{% embed url="https://www.winhistory.de/more/winstart/winstart.htm.en" caption="这里可以找到windows所有版本的开机音乐" %}

```bash
cd ~
mkdir .music && cd "$_"
wget 'https://www.winhistory.de/more/winstart/mp3/vista.mp3' -O /home/pi/.music/vista.mp3
/usr/bin/omxplayer /home/pi/.music/vista.mp3
cd /usr/lib/systemd/system/
sudo touch startupmusic.service
```

{% code-tabs %}
{% code-tabs-item title="/usr/lib/systemd/system/startupmusic.service" %}
```bash
[Unit]
Description= start-up-music-service
After=rc-local.service

[Service]
Type=simple
User=root
Group=root
WorkingDirectory=/home
ExecStart=/usr/bin/omxplayer /home/pi/.music/vista.mp3

Restart=oneshot

[Install]
WantedBy=multi-user.target
```
{% endcode-tabs-item %}
{% endcode-tabs %}

```bash
sudo systemctl start  startupmusic.service
sudo systemctl enable  startupmusic.service
```

