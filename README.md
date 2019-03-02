"# django2_learn" 

#### [Django 2.1 中文文档](https://docs.djangoproject.com/zh-hans/2.1/intro/overview/)


#### Python与Django对应表

参考:

[What Python version can I use with Django?](https://docs.djangoproject.com/en/dev/faq/install/#what-python-version-can-i-use-with-django)

|Python版本|Django版本|
|---|-----|
1.11|2.7, 3.4, 3.5, 3.6, 3.7 (added in 1.11.17)
2.0|3.4, 3.5, 3.6, 3.7
2.1, 2.2|	3.5, 3.6, 3.7
3.0|3.6, 3.7, 3.8

编写你的第一个 Django 应用
----------

安装

`pip install django==2.1.7`

查看版本

`python -m django --version`

创建项目

`django-admin startproject mysite`

目录结构:

```
└─mysite
    │  manage.py:
    │
    └─mysite
            settings.py
            urls.py
            wsgi.py
            __init__.py

```

- manage.py: 
    - 一个让你用各种方式管理 Django 项目的命令行工具。你可以阅读 [django-admin and manage.py](https://docs.djangoproject.com/zh-hans/2.1/ref/django-admin/) 获取所有 manage.py 的细节。
- mysite/ 
    - 目录包含你的项目，它是一个纯 Python 包。它的名字就是当你引用它内部任何东西时需要用到的 Python 包名。 (比如 mysite.urls).
- mysite/__init__.py：
    - 一个空文件，告诉 Python 这个目录应该被认为是一个 Python 包。如果你是 Python 初学者，阅读官方文档中的 [更多关于包的知识](https://docs.python.org/3/tutorial/modules.html#tut-packages)。
- mysite/settings.py：
    - Django 项目的配置文件。如果你想知道这个文件是如何工作的，请查看 [Django settings](https://docs.djangoproject.com/zh-hans/2.1/topics/settings/) 了解细节。
- mysite/urls.py：
    - Django 项目的 URL 声明，就像你网站的“目录”。阅读 [URL调度器](https://docs.djangoproject.com/zh-hans/2.1/topics/http/urls/) 文档来获取更多关于 URL 的内容。
- mysite/wsgi.py：
    - 作为你的项目的运行在 WSGI 兼容的Web服务器上的入口。阅读 [如何使用 WSGI 进行部署](https://docs.djangoproject.com/zh-hans/2.1/howto/deployment/wsgi/) 了解更多细节。
    
确认一下 Django 创建成功了:

```
python manage.py runserver
```

创建投票应用

```
python manage.py startapp polls
```

##### 项目 VS 应用

> 项目和应用有啥区别？应用是一个专门做某件事的网络应用程序——比如博客系统，或者公共记录的数据库，或者简单的投票程序。项目则是一个网站使用的配置和应用的集合。项目可以包含很多个应用。应用可以被很多个项目使用。

#### urls.py

函数 path() 的四个参数:

- route(必须有)
    - route 是一个匹配 URL 的准则（类似正则表达式）。当 Django 响应一个请求时，它会从 urlpatterns 的第一项开始，按顺序依次匹配列表中的项，直到找到匹配的项。(不会匹配 GET 和 POST 参数或域名)
- view(必须有)
    - 当 Django 找到了一个匹配的准则，就会调用这个特定的视图函数，并传入一个 HttpRequest 对象作为第一个参数，被“捕获”的参数以关键字参数的形式传入。
- kwargs
    - 任意个关键字参数可以作为一个字典传递给目标视图函数。
- name 
    -   为你的 URL 取名能使你在 Django 的任意地方唯一地引用它，尤其是在模板中。这个有用的特性允许你只改一个文件就能全局地修改某个 URL 模式。
    
#### 数据库配置

settings.py

```djangourlpath
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}
```

使用 `SQLite` 作为默认数据库。如果想使用其他数据库，你需要安装合适的 [database bindings](https://docs.djangoproject.com/zh-hans/2.1/topics/install/#database-installation) ，然后改变设置文件中 DATABASES 'default' 项目中的一些键值：

- `ENGINE` -- 
    - 可选值有 `'django.db.backends.sqlite3'`，`'django.db.backends.postgresql'`，`'django.db.backends.mysql'`，或 `'django.db.backends.oracle'`
- NAME 
    - 数据库的名称。如果使用的是 `SQLit`，数据库将是你电脑上的一个文件

> 如果你不使用 `SQLite`，则必须添加一些额外设置，比如 `USER` 、 `PASSWORD` 、 `HOST` 等等。想了解更多数据库设置方面的内容，请看文档：[DATABASES](https://docs.djangoproject.com/zh-hans/2.1/ref/settings/#std:setting-DATABASES) 。

> 另外，还要确保该数据库用户中提供 `mysite/settings.py` 具有 `"create database"` 权限

默认开启的某些应用需要至少一个数据表，所以，在使用他们之前需要在数据库中创建一些表。请执行以下命令：

```
python manage.py migrate
```

> 这个 migrate 命令检查 INSTALLED_APPS 设置，为其中的每个应用创建需要的数据表，至于具体会创建什么，这取决于你的 mysite/settings.py 设置文件和每个应用的数据库迁移文件（我们稍后会介绍这个）。这个命令所执行的每个迁移操作都会在终端中显示出来。

##### INSTALLED_APPS 

> 关注一下文件头部的 INSTALLED_APPS 设置项。这里包括了会在你项目中启用的所有 Django 应用。应用能在多个项目中使用，你也可以打包并且发布应用，让别人使用它们。

默认包括了以下 Django 的自带应用：

- django.contrib.admin 
	- 管理员站点
- django.contrib.auth 
	- 认证授权系统
- django.contrib.contenttypes 
	- 内容类型框架
- django.contrib.sessions 
	- 会话框架
- django.contrib.messages 
	- 消息框架
- django.contrib.staticfiles 
	- 管理静态文件的框架
	
