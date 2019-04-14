---
description: 杂七杂八的便捷脚本汇总
---

# 莫名其妙的趣味

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

## 全局语音服务脚本

```bash
#!/usr/bin/python
# _*_ coding=utf-8 _*_
import sys
import os

reload(sys)
sys.setdefaultencoding('utf-8')
import logging

logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(name)s - %(levelname)s - %(message)s')
logger = logging.getLogger(__name__)
try:
    import requests
except ImportError:
    sys.exit("""You need requests!
                or run pip install requests.""")
try:
    from pydub import AudioSegment
    from pydub.playback import play
except ImportError:
    sys.exit("""You need pydub!
                or run pip install pydub.""")
import hashlib
import json
import argparse

parser = argparse.ArgumentParser()
parser.add_argument('--text', help='text you want to say')
args = parser.parse_args()
text = args.text
try:
    assert text is not None
except AssertionError:
    print 'speech --text WhatYouWantToSay'
    exit(-1)

__all__ = ['get_voice']
apiKey = ""
secretKey = ""


# /**
#  * 百度语音的配置项
#  * Date 2017/12/10 上午1:02
#  */
class Voice(object):
    def __init__(self, text, lang='zh', speed=5, pit=5, vol=5, per=4):
        #  * 合成的文本，使用UTF-8编码，请注意文本长度必须小于1024字节	是
        self.tex = text
        #  * 语言选择,填写zh	是
        self.lang = lang
        #  * 客户端类型选择，web端填写1	是
        self.ctp = '1'
        #  * 用户唯一标识，用来区分用户，填写机器 MAC 地址或 IMEI 码，长度为60以内	否
        self.cuid = 'abc'
        #  * 语速，取值0-9，默认为5中语速	否
        self.spd = speed
        #  * 音调，取值0-9，默认为5中语调	否
        self.pit = pit
        #  * 音量，取值0-15，默认为5中音量	否
        self.vol = vol
        #  * 发音人选择, 0为女声，1为男声，3为情感合成-度逍遥，4为情感合成-度丫丫，默认为度丫丫，	否
        self.per = per

    def data(self):
        # type: () -> dict
        return {
            'lan': self.lang,
            'ctp': self.ctp,
            'cuid': self.cuid,
            # usually custom define
            'tex': self.tex,
            'vol': self.vol,
            'pit': str(self.pit),
            'per': str(self.per),
            'spd': str(self.spd)
        }


def get_token():
    getTokenURL = "https://openapi.baidu.com/oauth/2.0/token?grant_type=client_credentials&client_id=%s&client_secret=%s" % (
        apiKey, secretKey)
    r = requests.get(getTokenURL)
    return json.loads(r.text)['access_token']


def get_voice(string, filename='temp.mp3'):
    url = 'http://tsn.baidu.com/text2audio'
    data = Voice(string).data()
    data['tok'] = get_token()
    r = requests.get('%s?%s' % (url, '&'.join(['%s=%s' % (k, v) for k, v in data.items()])))
    if r.headers['Content-Type'] == 'audio/mp3':
        save_requests_to_file(r, filename)
    else:
        raise Exception, r.text


def save_requests_to_file(resp, filename):
    open(filename, 'wb').write(resp.content)


if __name__ == '__main__':
    cache_path = os.path.join('/tmp', '.voice_cache')

    if not os.path.exists(cache_path):
        print 'mkdir voice cache dir', cache_path
        os.makedirs(cache_path)

    text = text.strip().encode('utf-8')
    md5 = hashlib.md5(text).hexdigest()
    music_name = '%s.mp3' % md5
    music_path = os.path.join(cache_path, music_name)
    if not os.path.exists(music_path):
        get_voice(text, filename=music_path)
        logger.info('cache music to %s' % music_path)
    else:
        logger.info('use cache music at %s' % music_path)
    song = AudioSegment.from_mp3(music_path)
    play(song)
```

## 网络检查

```bash
if ping -q -c 1 -W 1 8.8.8.8 >/dev/null; then
  echo "Network is ok"
else
  say --text '断网了'
fi
```

{% hint style="info" %}
这里吧比较容易纠结,如果断网就发消息或者广播,太烦人了,所以暂时的方案是在白天进行检查,同时,控制cron任务的频率
{% endhint %}

