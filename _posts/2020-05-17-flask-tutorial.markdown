---
layout: post
title: flask-tutorial
date: 2020-05-17 00:00:00 +0300
description: flask 메가 튜토리얼
img: flask.png #
tags: [flask, python]
---

## Requirements
   * python3.4 이상  
   * editor pycharm(JetBrains PyCharm Community Edition)


## 튜토리얼 번역 및 따라하기
[Tutorial][Tutorial] 에서 내용 코드 및 설명(영어) 확인 가능

* 튜토리얼 작성자 : Miguel Grinberg


## Python 설치 확인

cmd 실행
python 또는 python3 입력

{% highlight ruby %}
> $ python3
> $ Python 3.5.2 (default, Nov 17 2016, 17:05:23)
> $[GCC 5.4.0 20160609] on linux
> $ Type "help", "copyright", "credits" or "license" for more information.
>
{% endhighlight %}

* 위처럼 뜬다면 파이썬 설치가 되어 있는 경우 위와 같이 뜨고 아닌 경우 파이썬을 먼저 설치해주세요. (파이썬 interactive prompt 종료 -> ctrl + c 또는 ctrl + z  )

{% highlight ruby %}
> $ mkdir microblog
> $ cd microblog
> $ python3 -m venv venv
{% endhighlight %}

* 프로젝트 디렉토리(폴더) 생성 후 해당 디렉토리도 이동 후에 파이썬 가상환경 생성
* python3 -m venv venv = 파이썬에게 venv 패키지를 실행시키며 venv 에게 venv 이름의 가상환경을 생성시키도록 함(python 3.4 이상부터 가능)
* virtualenv venv 로도 생성 가능 (python 3.4 보다 낮은 버전)

{% highlight ruby %}
> $ source venv/bin/activate
> $ venv\Scripts/activate (윈도우의 경우)
> (venv) $ 
{% endhighlight %}

* 위의 명령어로 가상환경으로 들어감/ 나올떄는 $ deactivate
* flask 설치
* `__init__.py` 는 이 파일이 위치한 디렉토리를 파이썬 패키지로 인식하여 파이썬이 읽을 수 있도록 해줌(import) 

{% highlight ruby %}
> $ (venv) pip install flask
> $ (venv) mkdir app
> $ (venv) cd app
> $ (venv) nano __init__.py
{% endhighlight %}


{% highlight ruby %}
> # app/__init__.py
> from flask import Flask
>
> app = Flask(__name__)
> print(app) # <Flask 'app'> 
>
> from app import routes
{% endhighlight %}

* from flask import Flask = flask 에서 import된 Flask 인스턴스로서 앱 오브젝트를 생성한다.
* app = Flask(`__name__`) = `__name__` 은 해당 모듈(app)을 Flask에 전달해주며 모듈의 다른 위치에서 이 파일을 기준으로 템플릿 파일 등의 소스들을 로드시 시작점으로 사용함
* from app import routes = import 부분이 상단이 아닌 하단에 있는 이유는 circular imports 이며 실제 routes 에서 app 을 import 하기 때문에 하단에 위치함으로써 에러를 방지함
* 만약에 상단에 있다면 routes 로 가서 import 하는데 바로 app 으로 다시 가고 다시 routes 이동하는 문제
* 하단에 있어서 app 이 먼저 생성되고 routes를 import 할때 그 app 인스턴트가 import 된다


{% highlight ruby %}
> # app/routes.py
> from app import app

> @app.route('/')
> @app.route('/index')
> def index():
>    return "Hello, World!"
{% endhighlight %}
* routes 들은 앱이 실행하는 다른 url들이며, view functions 이고, 사용자가 url 로 접근 시 flask가 어떤 로직을 실행해야 하는지 알려줌.(routes에서 실행되는 함수) 
* @app.route - 데코레이터를 통해 주어진 url 인자와 함수 index() 를 받고, 사용자가 해당 url로 요청하면 함수를 실행하고 return 한다. 

{% highlight ruby %}
> # microblog.py
> from app import app
{% endhighlight %}
* from app = 모듈 app, import app = flask 가 생성한 app 인스턴스

{% highlight ruby %}
> $ microblog/
> $  venv/
> $  app/
> $    __init__.py
> $    routes.py
> $  microblog.py
{% endhighlight %}
* 지금까지의 디렉토리 구조

{% highlight ruby %}
> $ (ven) export FLASK_APP=microblog.py
> $ (ven) set FLASK_APP=microblog.py   - 윈도우
{% endhighlight %}
* flask 에게 실행할 앱 등록
* flask 에게 실행할 앱 다른 방법 1. dotenv 활용
{% highlight ruby %}
    > $ (ven) pip install python-dotenv
    > $ (ven) nano .flaskenv
    > FLASK_APP=microblog.py
{% endhighlight %}

{% highlight ruby %}
> $ (ven) flak run 
{% endhighlight %}





[Tutorial]:https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world