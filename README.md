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
	
#### 创建模型

> 在 Django 里写一个数据库驱动的 Web 应用的第一步是定义模型 - 也就是数据库结构设计和附加的其它元数据。

> 模型是真实数据的简单明确的描述。它包含了储存的数据所必要的字段和行为。Django 遵循 DRY Principle 。它的目标是你只需要定义数据模型，然后其它的杂七杂八代码你都不用关心，它们会自动从模型生成。

#### 激活模型

> 为了在我们的工程中包含这个应用，我们需要在配置类 INSTALLED_APPS 中添加设置。
> 因为 PollsConfig 类写在文件 polls/apps.py 中，所以它的点式路径是 'polls.apps.PollsConfig'。在文件 mysite/settings.py 中 INSTALLED_APPS 子项添加点式路径后，它看起来像这样：
```djangotemplate
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'polls.apps.PollsConfig',
]
```

`python manage.py makemigrations polls`

> 通过运行 makemigrations 命令，Django 会检测你对模型文件的修改（在这种情况下，你已经取得了新的），并且把修改的部分储存为一次 迁移。
> 迁移是 Django 对于模型定义（也就是你的数据库结构）的变化的储存形式 - 没那么玄乎，它们其实也只是一些你磁盘上的文件。如果你想的话，你可以阅读一下你模型的迁移数据，它被储存在 polls/migrations/0001_initial.py 里。别担心，你不需要每次都阅读迁移文件，但是它们被设计成人类可读的形式，这是为了便于你手动修改它们。
> Django 有一个自动执行数据库迁移并同步管理你的数据库结构的命令 - 这个命令是 `migrate`，我们马上就会接触它 - 但是首先，让我们看看迁移命令会执行哪些 SQL 语句。sqlmigrate 命令接收一个迁移的名称，然后返回对应的 SQL：

` python manage.py sqlmigrate polls 0001`


### 遇到的问题

Django数据迁移出错

```
django.db.migrations.exceptions.MigrationSchemaMissing: Unable to create the django_migrations table ((1064, "You have an error in your SQL syntax; check the manual that correspond
s to your MySQL server version for the right syntax to use near '(6) NOT NULL)' at line 1"))
```

解决:

Django2.1 版本不再支持 MySQL5.5,升级MySQL版本

### 初试API 

进入Python命令行,
```
python manage.py shell

>>> from polls.models import Choice, Question  # Import the model classes we just wrote.

# No questions are in the system yet.
>>> Question.objects.all()
<QuerySet []>

# Create a new Question.
# Support for time zones is enabled in the default settings file, so
# Django expects a datetime with tzinfo for pub_date. Use timezone.now()
# instead of datetime.datetime.now() and it will do the right thing.
>>> from django.utils import timezone
>>> q = Question(question_text="What's new?", pub_date=timezone.now())

# Save the object into the database. You have to call save() explicitly.
>>> q.save()

# Now it has an ID.
>>> q.id
1

# Access model field values via Python attributes.
>>> q.question_text
"What's new?"
>>> q.pub_date
datetime.datetime(2012, 2, 26, 13, 0, 0, 775217, tzinfo=<UTC>)

# Change values by changing the attributes, then calling save().
>>> q.question_text = "What's up?"
>>> q.save()

# objects.all() displays all the questions in the database.
>>> Question.objects.all()
<QuerySet [<Question: Question object (1)>]>
```


### 介绍 Django 管理页面

#### 创建一个管理员账号
```
python manage.py createsuperuser
```


##### 向管理页面中加入投票应用

> 投票应用在哪呢？它没在索引页面里显示。
> 只需要做一件事：我们得告诉管理页面，问题 Question 对象需要被管理。打开 polls/admin.py 文件，把它编辑成下面这样：

```djangotemplate
from django.contrib import admin

from .models import Question

admin.site.register(Question)
```

### 视图

> Django 中的视图的概念是「一类具有相同功能和模板的网页的集合」。比如，在一个博客应用中，你可能会创建如下几个视图：

博客首页——展示最近的几项内容。
- 内容“详情”页——详细展示某项内容。
- 以年为单位的归档页——展示选中的年份里各个月份创建的内容。
- 以月为单位的归档页——展示选中的月份里各天创建的内容。
- 以天为单位的归档页——展示选中天里创建的所有内容。
- 评论处理器——用于响应为一项内容添加评论的操作。

而在我们的投票应用中，我们需要下列几个视图：

- 问题索引页——展示最近的几个投票问题。
- 问题详情页——展示某个投票的问题和不带结果的选项列表。
- 问题结果页——展示某个投票的结果。
- 投票处理器——用于响应用户为某个问题的特定选项投票的操作。

在 Django 中，网页和其他内容都是从视图派生而来。每一个视图表现为一个简单的 Python 函数（或者说方法，如果是在基于类的视图里的话）。Django 将会根据用户请求的 URL 来选择使用哪个视图（更准确的说，是根据 URL 中域名之后的部分）。

### 模板

页面的设计写死在视图函数的代码里的。如果你想改变页面的样子，你需要编辑 Python 代码。所以让我们使用 Django 的模板系统，只要创建一个视图，就可以将页面的设计从代码中分离出来。

首先，在你的 polls 目录里创建一个 templates 目录。Django 将会在这个目录里查找模板文件。

你项目的 [TEMPLATES](https://docs.djangoproject.com/zh-hans/2.1/ref/settings/#std:setting-TEMPLATES) 配置项描述了 Django 如何载入和渲染模板。默认的设置文件设置了 DjangoTemplates 后端，并将 APP_DIRS 设置成了 True。这一选项将会让 DjangoTemplates 在每个 [INSTALLED_APPS](https://docs.djangoproject.com/zh-hans/2.1/ref/settings/#std:setting-INSTALLED_APPS) 文件夹中寻找 "templates" 子目录。这就是为什么尽管我们没有像在第二部分中那样修改 DIRS 设置，Django 也能正确找到 polls 的模板位置的原因。

在你刚刚创建的 templates 目录里，再创建一个目录 polls，然后在其中新建一个文件 index.html 。换句话说，你的模板文件的路径应该是 polls/templates/polls/index.html 。因为 Django 会寻找到对应的 app_directories ，所以你只需要使用 polls/index.html 就可以引用到这一模板了。

模板命名空间:

> 虽然我们现在可以将模板文件直接放在 polls/templates 文件夹中（而不是再建立一个 polls 子文件夹），但是这样做不太好。Django 将会选择第一个匹配的模板文件，如果你有一个模板文件正好和另一个应用中的某个模板文件重名，Django 没有办法 区分 它们。我们需要帮助 Django 选择正确的模板，最简单的方法就是把他们放入各自的 命名空间 中，也就是把这些模板放入一个和 自身 应用重名的子文件夹里。

##### 一个快捷函数： render()

>「载入模板，填充上下文，再返回由它生成的 HttpResponse 对象」是一个非常常用的操作流程。于是 Django 提供了一个快捷函数，我们用它来重写 index() 视图：

##### 抛出 404 错误

尝试用 get() 函数获取一个对象，如果不存在就抛出 Http404 错误也是一个普遍的流程。Django 也提供了一个快捷函数，下面是修改后的详情 detail() 视图代码：

```
def detail(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, 'polls/detail.html', {'question': question})
```

> 为什么我们使用辅助函数 get_object_or_404() 而不是自己捕获 ObjectDoesNotExist 异常呢？还有，为什么模型 API 不直接抛出 ObjectDoesNotExist 而是抛出 Http404 呢？
> 因为这样做会增加模型层和视图层的耦合性。指导 Django 设计的最重要的思想之一就是要保证松散耦合。一些受控的耦合将会被包含在 django.shortcuts 模块中。