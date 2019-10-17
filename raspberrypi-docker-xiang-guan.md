# Raspberrypi Docker 相关

{% hint style="info" %}
Thanks to @link

[http://shumeipai.nxez.com/2019/05/20/how-to-install-docker-on-your-raspberry-pi.html](http://shumeipai.nxez.com/2019/05/20/how-to-install-docker-on-your-raspberry-pi.html)
{% endhint %}

```bash
sudo curl -sSL https://get.docker.com | sh
```

如果报错如下类似

```bash
E: The repository 'http://archive.raspberrypi.org/debian buster Release' no longer has a Release file.
E: The repository 'http://raspbian.raspberrypi.org/raspbian buster Release' no longer has a Release file.
```

则

```bash
sudo apt-get upgrade 
# 可能会挺慢的,大概十分钟-二十分钟的样子
```

然后再执行docker 安装脚本就莫得问题了.

### Web 管理 docker - Portainer

部署一个portainer 的 container

{% embed url="https://github.com/portainer/portainer" caption="portainer 项目介绍" %}

```bash
sudo docker run -d -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock --restart always --name portainer portainer/portainer -H unix:///var/run/docker.sock
```

{% code-tabs %}
{% code-tabs-item title="安装成功的输出\(节选最后\)" %}
```bash
If you would like to use Docker as a non-root user, you should now consider
adding your user to the "docker" group with something like:

  sudo usermod -aG docker pi

Remember that you will have to log out and back in for this to take effect!

WARNING: Adding a user to the "docker" group will grant the ability to run
         containers which can be used to obtain root privileges on the
         docker host.
         Refer to https://docs.docker.com/engine/security/security/#docker-daemon-attack-surface
         for more information.
```
{% endcode-tabs-item %}
{% endcode-tabs %}

