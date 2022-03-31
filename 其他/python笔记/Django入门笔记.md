# 使用Django创建Web应用程序的步骤

## 一. 建立项目

### 1. 制定任务目标

### 2. 建立虚拟环境

```
python -m venv ll_env
```

这里使用模块venv创建了虚拟环境ll_env

### 3. 激活虚拟环境

```
source ll_env/bin/activate
```

停止使用虚拟环境：

```
deactivate
```

### 4. 安装Django

```
pip3 install Django
```

### 5. 在Django中创建项目

```
django-admin.py startproject learning_log .
```

这里要在虚拟环境中，即(ll_env)在前面

命令末尾的句点会创建合适的目录结构，便于后续操作

### 6. 创建数据库

```
python manage.py migrate
```

### 7. 查看项目(可选)

```
python manage.py runsever
```

这里Django将启动一个服务器，在浏览器中输入URL: [http://localhost:8000/](http://localhost:8000/) 即可打开网页

## 二. 创建应用程序

这里要切换到manage.py所在的目录，执行:

```
python manage.py startapp learning_logs
```

### 1. 定义模型

修改models.py为：

```python
form django.db import models

class Topic(models.Model):
    """用户学习的主题"""
    topic_text = models.CharField(max_length = 200)
    date_added = models.DateTimeField(auto_now_add = True)
    
    def __str__(self):
        """返回模型的字符串表示"""
        return self.topic_text

class Entry(models.Model):
    """学到的有关某个主题的具体知识"""
    topic = models.ForeignKey(Topic, on_delete=models.CASCADE)
    entry_text = models.TextField()
    date_added = models.DateTimeField(auto_now_add = True)
    
    class Meta:
        verbose_name_plural = 'entries'
    
    def __str__(self):
        """返回模型的字符串表示"""
        return self.entry_text[:50] + "..."
```

这里创建了一个Topic类，有两个属性：text和date_added

`__str__()`方法用于显示模型的简单表示

### 2. 激活模型

打开settings.py, 将`INSTALLED_APPS`修改成：

```python
--snip-- 
INSTALLED_APPS = (
	--snip-- 
	'django.contrib.staticfiles',
	# 我的应用程序
	'learning_logs',
) 
--snip--
```

让Django修改数据库：

```
python manage.py makemigrations learning_logs
```

让Django迁移项目：

```
python manage.py migrate
```

每当修改项目管理的数据时，都要采取这三个步骤：

1. 修改models.py;
2. python manage.py makemigrations *app_name*; 
3. python manage.py migrate.

### 3. Django管理网站

 #### 1. 创建超级用户

```
python manage.py createsuperuser
```

Django并不存储你输入的密码，而存储从该密码派生出来的一个字符串——散列值。每当你输入密码时，Django都计算其散列值，并将结果与存储的散列值进行比较。如果这两个散列值相同，就通过了身份验证。 即便黑客获得了网站数据库的访问权，也只能获取其中存储的散列值， 而无法获得密码。在网站配置正确的情况下，几乎无法根据散列值推导出原始密码。

 #### 2. 向管理网站注册刚才的模型

将admin.py修改成：

```python
from django.contrib import admin

from learning_logs.models import Topic, Entry

admin.site.register(Topic)
admin.site.register(Entry)
```

可登陆管理网站查看:[http://lcoalhost:8000/admin/](http://localhost:8000/admin/)

同样地，每次添加新模型都要这样注册

## 三. 创建网页






