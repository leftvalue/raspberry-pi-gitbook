---
description: 零散记录
---

# Python

## 装包有坑

#### Pillow 无法安装

```bash
sudo apt-get install libjpeg8-dev zlib1g-dev  liblcms2-dev libwebp-dev tcl8.5-dev tk8.5-dev
```

## 安装 python 3.7.4 

{% embed url="https://gist.github.com/SeppPenner/6a5a30ebc8f79936fa136c524417761d" %}

{% code-tabs %}
{% code-tabs-item title="setup.sh" %}
```bash
sudo apt-get update -y
sudo apt-get install build-essential tk-dev libncurses5-dev libncursesw5-dev libreadline6-dev libdb5.3-dev libgdbm-dev libsqlite3-dev libssl-dev libbz2-dev libexpat1-dev liblzma-dev zlib1g-dev libffi-dev -y
wget https://www.python.org/ftp/python/3.7.4/Python-3.7.4.tar.xz
tar xf Python-3.7.4.tar.xz
cd Python-3.7.4
./configure
make -j 4
sudo make altinstall
cd ..
sudo rm -r Python-3.4.0
rm Python-3.7.4.tar.xz
sudo apt-get --purge remove build-essential tk-dev libncurses5-dev libncursesw5-dev libreadline6-dev libdb5.3-dev libgdbm-dev libsqlite3-dev libssl-dev libbz2-dev libexpat1-dev liblzma-dev zlib1g-dev libffi-dev -y
sudo apt-get autoremove -y
sudo apt-get clean
```
{% endcode-tabs-item %}
{% endcode-tabs %}



