# 2 Python-Flask

## 1. Virtualenv虚拟环境

是一个互相隔离的目录，因为我们可能用到不同版本的环境

```
mkvirtualenv flask_py
mkvirtualenv flask_py -p python3  // p: path
workon flask_py // 切换虚拟环境
deactivate // 退出虚拟环境

pip install flask=0.10.1
pip list // 查看安装了什么安装包
```



### 1.2 pip是什么？

pip 是 Python 包管理工具

```
sudo apt-get install python-pip

```



### 1.3 site-package是什么？

python包的存储



### 1.4 别人下载了你的项目，如何安装依赖包？

**1.首先备份**

```
pip freeze > requirements.txt
```

**2.恢复，别人执行即可安装相应的依赖**

```
pip install -r requirements.txt
python3 -m pip install nodejs // 指定python3的pip安装
```



## 2. Python的Web框架

Django、Flask



## 3.常用包

```
import requests
import datetime
import time
import json
import logging
from logging.handlers import RotatingFileHandler
```



### 日志

```python
import logging
from logging.handlers import RotatingFileHandler

# log
LOG_FORMAT = "%(asctime)s - %(levelname)s - %(message)s"
logging.basicConfig(level=logging.INFO, format=LOG_FORMAT)
HANDLERS = RotatingFileHandler(filename='auto_archery.log', maxBytes=100*1024, backupCount=1)
logging.getLogger().addHandler(HANDLERS)

logging.info('=======================================================================================')
logging.info('start login %s', 'https://bing.com')
```



### 时间日期

```python
import datetime
import time

datetime.date.today()
logging.info('sleep 10 minutes, now time: %s', datetime.datetime.now().strftime('%Y-%m-%d %H:%M:%S'))

# 单位s
time.sleep(10 * 60)
```



### HTTP

```python
import requests
import json

data = {
    'limit': LIMIT,
    'offset': offset,
    'navStatus': 'workflow_manreviewing',
    'instance_id': '',
    'group_id': '',
    'start_date': date_time,
    'end_date': date_time,
    'search': '',
}
headers['Sec-Fetch-Site'] = 'same-origin'
# 请求行，请求头，请求体
response = requests.post(SQL_WORK_FLOW_URL, headers=headers, data=data)
# json
josn = response.json()
mydata = json.dumps(data)
```

