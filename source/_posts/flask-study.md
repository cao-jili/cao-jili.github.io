---
title: 快速上手flask
date: 2023-02-02 15:12:50
tags: ['flask','python']
index_img: /img/flask-study.png
---

## 环境搭建
1. 进入项目根目录，构建虚拟环境，生成名称为venv的环境，可以看到生成了venv文件夹
     ``` python3 -m venv venv ```
2. 激活虚拟环境(使用 `deactivate`退出虚拟环境)
    ```. venv/bin/activate```
3. 安装flask, 遇到安装不了的情况可以尝试更换安装源
    ``` pipe install flask```
    更换安装源
    ```pip install flask -i http://pypi.douban.com/simple/ --trusted-host pypi.douban.com```
## 项目结构
### 1. 依赖管理
requirement.txt 
    * 安装文件中的依赖 `pip install -r requirements.txt`
    * 环境中的依赖写入文件 `pip freeze >requirements.txt`
```
Flask==2.2.2
```
### 2. 配置文件
config.py
```
import os

class Config:
    SECRET_KEY = os.environ.get('SECRET_KEY') or 'hard to guess string'

    @staticmethod
    def init_app(app):
        pass


class DevelopmentConfig(Config):
    DEBUG = True
    DB_URL = ''


class ProductConfig(Config):
    DB_URL = ''


config = {
    'development': DevelopmentConfig,
    'production': ProductConfig,
    'default': DevelopmentConfig
}

```
### 3. 使用蓝图创建路由
router.py
```
from flask import Blueprint

user = Blueprint('user', __name__)


@user.app_errorhandler(404)
def page_not_found(e):
    return '404', 404


@user.route('/user/<name>', methods=['get'])
def get_name(name):
    return 'name = {name}'.format(name=name)
```
### 4. 入口文件
flaskdemo.py
```
from flask import Flask
from config import config
from .userrouter import user
import os


def create_app(config_name):
    app = Flask(__name__)
    app.config.from_object(config[config_name])
    config[config_name].init_app(app)
    app.register_blueprint(user)
    return app

app = create_app(os.getenv('FLASK_CONFIG') or 'default')
```
### 5. 运行应用
启动服务，'0.0.0.0' 使服务被外网访问到
```
$ export FLASK_APP=flaskdemo.py
$ export FLASK_DEBUG=1
$ flask run -h '0.0.0.0'
```
## Docker部署
Flask自带的Web开发服务器不够稳健、安全和高效，不适合在生产环境中使用

