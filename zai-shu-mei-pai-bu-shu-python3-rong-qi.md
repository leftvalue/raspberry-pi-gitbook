# 在树莓派部署 python3 容器



{% embed url="https://hub.docker.com/\_/python" caption="https://hub.docker.com/\_/python" %}

官方镜像库已经写的很完备了,下面是一个简单的 flask server 例子

{% code-tabs %}
{% code-tabs-item title="Dockerfile" %}
```text
FROM python:3.7-slim-stretch
WORKDIR /usr/src/app
COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
EXPOSE 8089
CMD [ "python", "./app.py" ]
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="requirements.txt" %}
```text
requests
Flask
tornado
beautifulsoup4
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="app.py" %}
```python
# _*_ coding=utf-8 _*_
from flask import Flask
from tornado.wsgi import WSGIContainer
from tornado.httpserver import HTTPServer
from tornado.ioloop import IOLoop

app = Flask(__name__)


@app.route('/weibo')
def hello_world():
    import requests
    from bs4 import BeautifulSoup
    r = requests.get("https://s.weibo.com/top/summary?cate=realtimehot",
                     headers={
                         "User-Agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:71.0) "
                                       "Gecko/20100101 Firefox/71.0"
                     },
                     )
    doc = BeautifulSoup(r.text, 'html.parser')
    baseurl = "https://s.weibo.com"
    table = doc.select('#pl_top_realtimehot')[0]
    for a in table.select('a'):
        href = a.attrs['href']
        if href.startswith('/'):
            a.attrs['href'] = baseurl + href
    for face in table.select('img.face'):
        face.replace_with(face.attrs['title'])
    table.attrs['style'] = 'width:100%;'
    return table.prettify()


http_server = HTTPServer(WSGIContainer(app))
http_server.listen(8089)
IOLoop.instance().start()
```
{% endcode-tabs-item %}
{% endcode-tabs %}

上面三个文件放在同一目录下,执行

```bash
docker build -t flaskappimg .

docker run -it --rm -p 8089:8089 --name flaskcontainer flaskappimg 
# 后台常驻
docker run -d -p 8089:8089 --restart=always --name flaskcontainer  flaskappimg
```

