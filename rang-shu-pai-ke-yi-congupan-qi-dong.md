---
description: 听起来就不错哟
---

# 让树莓派可以从u盘启动

{% embed url="https://www.raspberrypi.org/documentation/hardware/raspberrypi/bootmodes/msd.md" %}

新版本的树莓派可以直接u盘启动,这里仅记录一下我的旧版3代树莓派,

首先,必须要在一个刻录了系统的tf卡下启动树莓派,

```bash
sudo apt-get update && sudo apt-get upgrade
# Then enable USB boot mode with this code:

echo program_usb_boot_mode=1 | sudo tee -a /boot/config.txt
# This adds program_usb_boot_mode=1 to the end of /boot/config.txt. Reboot the Raspberry Pi with sudo reboot, then check that the OTP has been programmed with:

vcgencmd otp_dump | grep 17:
# 如果出现 17:3020000a 说明设置成功
```

从此,可以从安装了系统的u盘启动\(第一顺位仍是 sd 卡\)

### 我知道你好奇 u盘 硬盘 以及 sd卡 哪种启动方式性能更强

{% embed url="https://www.raspberrypi.org/forums/viewtopic.php?t=199414" %}

结论 : 

1. SSD性能出色
2. microSD 略强于 USB flash



