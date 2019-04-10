# 智能音箱

{% embed url="https://wukong.hahack.com/\#/install?id=%e6%96%b9%e5%bc%8f%e4%ba%8c%ef%bc%9a%e6%89%8b%e5%8a%a8%e5%ae%89%e8%a3%85" %}

这里简单归纳一下

```bash
sudo apt-get install python-pyaudio python3-pyaudio sox pulseaudio libsox-fmt-all ffmpeg
sudo apt-get install portaudio19-dev python-pyaudio python3-pyaudio
pip3 install pyaudio
git clone https://github.com/wzpan/wukong-robot.git
cd wukong-robot
pip3 install -r requirements.txt
```

虽然 snowboy 不需要自己编译,但是 swig 还是要编译的

```bash
wget http://hahack-1253537070.file.myqcloud.com/misc/swig-3.0.10.tar.gz
tar xvf swig-3.0.10.tar.gz
cd swig-3.0.10
sudo apt-get -y update
sudo apt-get install -y libpcre3 libpcre3-dev
./configure --prefix=/usr --without-clisp --without-maximum-compile-warnings
make
sudo make install
sudo install -v -m755 -d /usr/share/doc/swig-3.0.10
sudo cp -v -R Doc/* /usr/share/doc/swig-3.0.10
sudo apt-get install -y libatlas-base-dev
```

二进制 snowboy so 文件

```bash
cd wukong 项目目录
cd snowboy
wget 'http://hahack-1253537070.file.myqcloud.com/misc/snowboy-pi/_snowboydetect.so'
```

官方文档蛮清楚的我就不赘述了,另外,个人的训练唤醒音频数据

{% embed url="https://github.com/leftvalue/pypi/tree/master/wukong-robot-resource" %}



