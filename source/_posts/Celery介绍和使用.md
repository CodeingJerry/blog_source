---
title: Celery介绍和使用
date: 2021-05-31 12:58:29
updated: 2021-05-31 12:58:29
tags: 
- Celery 
- 分布式任务
categories: 
- 编程技能提升 
- Python
---
### Celery应用场景
Celery 是一个 基于python开发的分布式异步消息任务队列，通过它可以轻松的实现任务的异步处理，举几个实例场景中可用的例子：

1. 你想对100台机器执行一条批量命令，可能会花很长时间 ，但你不想让你的程序等着结果返回，而是给你返回 一个任务ID,你过一段时间只需要拿着这个任务id就可以拿到任务执行结果， 在任务执行ing进行时，你可以继续做其它的事情。 

2. 你想做一个定时任务，比如每天检测一下你们所有客户的资料，如果发现今天 是客户的生日，就给他发个短信祝福。

   <!--more-->

### Celery基本流程
<img src="/images/celery/Celery执行流程图.jpg" align="center">

### 搭建运行环境
1. 系统版本
   `Ubuntu 18.04.5 LTS (GNU/Linux 4.15.0-143-generic x86_64)`

2. 更换国内源
   `sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak`
   `sudo nano /etc/apt/sources.list`
   ```
   # 删除sources.list内容，替换为以下内容
   deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
   deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
   deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
   deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
   deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
   deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
   deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
   deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
   deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
   deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
   ```
   `sudo apt-get update`

3. 安装redis-server
   ```
   sudo apt-get install redis-server 
   # 检查redis-server是否启动
   service redis status
   ```

4. 安装pip3
   `sudo apt install python3-pip`

5. 升级pip至20.2.1版本

   `sudo pip3 install --upgrade pip==20.2.1`

6. 安装 celery,redis
   ```
   pip install -U Celery==4.2.2
   pip install "celery[librabbitmq,redis,auth,msgpack]"
   ```

7. 安装 python-celery-common
   ```
   sudo apt update
   sudo apt install python-celery-common
   ```

8. 软件最终安装环境如下
   ```
   Ubuntu 18.04.5 LTS
   Python 3.6.9 
   pip==20.2.1
   redis 3.5.3
   celery-4.2.2
   amqp-5.0.6 
   billiard-3.6.4.0 
   cached-property-1.5.2 
   click-7.1.2 
   click-didyoumean-0.0.3 
   click-plugins-1.1.1 
   click-repl-0.1.6
   kombu-5.1.0 
   prompt-toolkit-3.0.18 
   vine-5.0.0 wcwidth-0.2.5
   ```

### Celery代码示例

#### 异步任务（Async Task）
- step1:创建一个celery_demo的python Package文件夹 在`__init__.py`中添加如下代码
```
from celery import Celery

#include:导入指定的任务模块
#这一次创建 app，并没有直接指定 broker(消息中间件来接收和发送任务消息) 和 backend(存储结果)。而是在配置文件中
app = Celery(
    'demo',
    include=[
        'celery_demo.task1',
        'celery_demo.task2',
    ]
)

# 通过celery实例加载配置模块
app.config_from_object(
    'celery_demo.celeryconfig',
)
```
- step2:在celery_demo文件夹下新建一个celeryconfig.py文件（Celery的配置文件） 添加如下代码
```
#broker(消息中间件来接收和发送任务消息)
broker_url = 'redis://localhost:6379/1'
#backend(存储worker执行的结果)
result_backend = 'redis://localhost:6379/2'

#设置时间参照，不设置默认使用的UTC时间
timezone = 'Asia/Shanghai'
#指定任务的序列化
task_serializer ='json'
#指定执行结果的序列化
result_serializer ='json'
accept_content=['json']  # Ignore other content
```
- step3:在celery_demo文件夹下新建task1.py和task2.py文件
```
# task1.py
import time
from celery_demo import app

@app.task
def add(x,y):
    time.sleep(5)
    return x+y
```
```
# task2.py
import time
from celery_demo import app

@app.task
def mut(x,y):
    time.sleep(5)
    return x*y
```
- step4:在celery_demo同级目录下新建app.py文件，代码如下，分别发送执行任务消息到broker
```
from celery_demo import task1,task2

#apply_async和delay都表示调用异步任务
task1.add.delay(2,4)
# task1.add.apply_async(2,4)
task2.mut.delay(3,4)

print('end...')
```
demo目录结构如下
```
app.py
celery_demo/
    __init__.py
    celeryconfig.py
    task1.py
    task2.py
```
- step5:进入celery_demo同级的目录，打开终端，启动worker
```
celery worker -A celery_demo -l INFO
```
<img src="/images/celery/Celery_worker.png" align="center">
再打开另一个终端，发送执行任务消息，进行如下操作
```
python3 app.py
```
<img src="/images/celery/Celery_worker_run.png" align="center">

#### 定时任务（Celery Beat）
- step1:在之前celery_demo文件夹下celeryconfig.py文件中，添加如下代码
```
from datetime import timedelta
from celery.schedules import crontab

#设置定时任务
beat_schedule = {
    'task1':{
        'task':'celery_demo.task1.add',
        'schedule':timedelta(seconds=30), #表示每30秒发送一次任务消息
        'args':(10,20)
    },
    'task2':{
        'task':'celery_demo.task2.mut',
        'schedule':crontab(hour=14,minute=25), #表示在每天的下午14点25分发送一次任务消息
        'args':(10,10)
    }
}
```
- step2:打开终端，启动定时消息任务，执行如下命令
```
celery beat -A celery_demo -l INFO
```
运行时报错如下
```
celery-beat KeyError: 'scheduler'
Removing corrupted schedule file 'celerybeat-schedule': error(22, 'Invalid argument')
......
```

### KeyError: 'scheduler'解决方法
```
   pip install celery-redbeat
   # celeryconfig.py文件添加
   app.conf.redbeat_redis_url = "redis://localhost:6379/3"
   # 用以下命令重新启动定时消息任务
   celery beat -A celery_demo -l INFO -S redbeat.RedBeatScheduler
```
<img src="/images/celery/beat.png" align="center">
对应worker端截图如下
<img src="/images/celery/beat_worker.png" align="center">

### 参考
[Celery介绍和基本使用](https://zhuanlan.zhihu.com/p/64595171)
[Celery 分布式任务队列入门](https://www.cnblogs.com/chenice/p/6918435.html)
[Celery 中文手册](https://www.celerycn.io/ru-men/celery-jian-jie)
[python之celery使用详解](https://www.cnblogs.com/cwp-bg/p/8759638.html)
[celery-beat KeyError: 'scheduler'](https://www.debugcn.com/en/article/39785884.html)

