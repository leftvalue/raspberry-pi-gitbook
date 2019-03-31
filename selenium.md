---
description: 树莓派上配置远程浏览器实例
---

# Selenium

#### Selenium Standalone Server

{% embed url="https://www.seleniumhq.org/download/" %}

#### Chromium-chromedriver

{% embed url="https://launchpad.net/ubuntu/trusty/+package/chromium-chromedriver" %}

```bash
sudo dpkg -i chromium-chromedriver_65.0.3325.181-0ubuntu0.14.04.1_armhf.deb
# 它将会安装到 
# /usr/lib/chromium-browser/chromedriver
# 为了后面 selenium 的console可以找到
ln -s /usr/lib/chromium-browser/chromedriver /usr/local/bin/chromedriver
```

### Then

```bash
java -jar selenium-server-standalone-3.141.jar -port 12345 -debug
```



