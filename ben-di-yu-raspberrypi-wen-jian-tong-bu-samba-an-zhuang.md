---
description: Samba
---

# 本地与raspberryPi 文件同步-Samba 安装

```bash
sudo apt-get install samba
sudo apt-get install samba-common-bin
sudo vi /etc/samba/smb.conf

# read only = yes” 改为 “read only = no
sudo smbpasswd -a pi
sudo smbd restart
```

```text
\\192.168.6.123\pi
```

