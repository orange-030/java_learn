# Django

## 框架

Model：模型；封装数据的交互操作

View：视图；用于将数据呈现给用户

Controller：控制器；接收用户输入了协调Model和View的关系

## 流程

Django中的流程为 MTV，本质上 MVC 的变形

Model：模型，同 MVC 中的 Model

Template：模版，只是HTML，充当 MVC 中 View 的角色

Views：视图函数，控制器将数据展示到视图中，相当于 MVC 中的Controller



#  QuickStart

## 创建虚拟环境和项目

```markdown
pip install django==2.2.14
pip install mysqlclient
django-admin startproject Project .

|—— manage.py # 整个项目的管理文件
|—— project —— __init__  # 整个项目的管理文件
|          |—— settings  # 全局项目配置文件
|          |—— urls      # 根路由
|          |—— wsgi      # 用于项目部署
```

## 修改配置文件

```
LANGUAGE_CODE = 'zh-hans'
TIME_ZONE = 'Asia/Shanghai'
USE_TZ = False
```

## 启动项目

```
manage.py runserver [ip: port]  # 使用开发服务器启动项目
```

## 创建应用

```markdown
manage.py startapp APP

|—— App —— __init__.py  
|      |—— views.py  # 视图函数
|      |—— models.py # 模型
|      |—— admin.py  # 后台管理
|      |—— apps.py   # 应用配置
|      |—— tests.py  # 单元测试
|      |—— urls.py   # 子路由
|      |—— migrations     
|                   |—— __init__.py
|                   |—— 0001_initial.py
|                   |—— 0002_initial.py
```

## 流程

1. 将项目注册到settings中的INSTALL_APPS中

2. 请求流程: Browser -->  urls --> views --> models --> views --> response

3.  在urls.py中编写一个路由

4.  在view.py中编写一个视图函数

   - 默认第一个参数为response
   - 必须返回一个response
     - HttpResponse
     - render

5. 连接数据库

   1. 在setting的DATABASES中修改数据库

   2. mysql

      ```mysql
      CREATE DATABASE app CHARSET=utf8;
      ```

      ```markdown
      - 驱动: mysqlclient
      - 配置: USER, PASSWORD, HOST, PORT, NAME, ENGINE
      ```

   3. 迁移
      
      - 生成迁移
      
        ```python
        manage.py makemigrations
        ```
      
      - 执行迁移
      
        ```python
        manage.py migrate  # 只有执行迁移后才会在数据库中产生表
        ```

   4. 数据操作
      - 增删改查 get(), delete()
      - 存储数据 save()

6. 显示在模版中

   - 在HTML中设置模版  {{ var }}
   - 传入字典参数
   - 渲染模版
------




# Model

## 基本概念

编译型语言：1. 将高级语言编译成机器识别的语言 2. 执行机器识别的语言

<img src="Image/Django/web_1.png" style="zoom:80%;" />

## 模型属性

属性 = models.字段属性

Django默认通过模型的objects对象实现模型数据查询

Django有两种过滤器用于筛选记录
filter：返回符合筛选条件的数据集
exclude：返回不符合筛选条件的数据集多个filter和exclude可以连接在一起查询

在模型中加入属性，需要迁移；加入方法，不需要迁移

**1. 字段属性**

|     字段名称     |                           字段说明                           |                            参数                            |
| :--------------: | :----------------------------------------------------------: | :--------------------------------------------------------: |
|    AutoField     |  ⼀个根据实际Id自动增长的IntegerField（通常不指定自动生成）  |                                                            |
|    CharField     |              字符串，默认的表单样式是TextInput               |                         max_length                         |
|   IntegerField   |                             整数                             |                                                            |
|    FloatField    |              ⽤Python的float实例来表示的浮点数               |                                                            |
|   BooleanField   |     true/false 字段，此字段的默认表单控制是CheckboxInput     |                                                            |
| NullBooleanField |                 ⽀持null、true、false三种值                  |                                                            |
|    DateField     | 使⽤Python的datetime.date实例表示的⽇期，<br/>该字段默认对应的表单控件是⼀个TextInput | auto_now和auto_now_add、default<br/>这三个参数不能同时共存 |

**2. 字段选项**

|  可选参数   |                             说明                             |
| :---------: | :----------------------------------------------------------: |
|    null     | 如果 True ，Django将 NULL 在数据库中存储空值。默认是 False 。<br/>不要在字符串字段上使用。null是数据库范畴的概念 |
|    blank    | 如果 True ，该字段允许为空。默认是 False 。同null不同，<br/>如果字段有 blank=True ，则表单验证将允许输⼊空值。如果字段有 blank=False ，则需要该字段。 |
|  db_column  | ⽤于此字段的数据库列的名称。如果没有给出，Django将使⽤该字段的名称。 |
|  db_index   |          如果 True ，将为此字段创建数据库常规索引。          |
|   unique    |         如果 True ，该字段在整个表格中必须是唯⼀的。         |
| primary_key |               如果 True ，此字段是模型的主键。               |
|   default   |            默认值 当前字段如果不给值则执行默认值             |



## 定义模型

数据库中的表名为：App_Model，如果想指定表名，可以在Meta类中设置db_table

如果没有指定主键，Django将⾃动给表创建⼀个⾃增⻓主键id

```python
id = models.AutoField（primary_key=True）
```

### Meta类中常用设置

| 名称     |                             说明                             |
| -------- | :----------------------------------------------------------: |
| db_table |                        数据库中的表名                        |
| abstract |            当设置为True时，说明当前模型是抽象基类            |
| managed  |      如果设置False，则迁移不会创建或删除表，默认是True       |
| ordering | 用于记录排序，ordering = ['pub_date']，降序ordering = ['-pub_date'] |

## 使用模型

### 增删改

```python
# 保存
user.save()
# 删除
user.delete()
# 逻辑删除
user = User.objects.get(pk=1)
user.is_delete = True
user.save()
```



### 查询方法

要从数据库检索数据，首先要获取一个查询集（QuerySet），查询集表示从数据库获取的对象集合，它可以有零个，一个或多个过滤器。返回查询集的方法，称为过滤器，过滤器根据给定的参数缩小查询结果范围，相当于sql语句中where或limit，也有一部分方法不返回查询集。

- 对于返回查询集的方法，可以顺序添加多个过滤器。
- 对于返回非查询集的方法，只能作为最后一个过滤器。

| 方法                        | 返回类型 | 说明                                                         |
| --------------------------- | -------- | ------------------------------------------------------------ |
| Model.objects.all()         | QuerySet | 返回表中所有数据                                             |
| Model.objects.filter()      | QuerySet | 返回符合条件的数据                                           |
| Model.objects.exclude()     | QuerySet | 返回不符合条件的数据                                         |
| Model.objects.order_by()    | QuerySet | 对查询结果集进行排序                                         |
| Model.objects.values()      | QuerySet | 返回⼀个Queryset，其中每个对象为⼀个字典                     |
| Model.objects.get()         | 模型对象 | 返回⼀个满足条件的对象；如果没有找到符合条件的对象，会引发模型类DoesNotExist异常; 如果找到多个，会引发模型类.MultiObjectsReturned 异常 |
| Model.objects.latest(field) | 模型对象 | 根据field字段返回最近增加记录                                |
| Model.objects.exists()      | bool     | 判断查询的数据是否存在                                       |
| Model.objects.count()       | int      | 返回查询集中对象的数目                                       |

对于查询集，也可以使用索引和切片方法

```python
User.objects.all()[10:25]  # 等同于 limit 15 offset 10
```

在过滤器的内部，还有字段查询方法

| 操作符   | 说明                         | 实例                                             |
| -------- | ---------------------------- | ------------------------------------------------ |
| exact/=  | 精确判等                     | username = 'admin'<br/>username__exact = 'admin' |
| iexact   | 不区分大小写判等             | username__iexact = 'admin'                       |
| contains | 模糊查询，等价like '%value%' | username__contains = 'admin'                     |
| in (id)  | 包含                         | user_id__in = [1, 2, 3]                          |
| gt/gte   | 大于/大于等于                | user_id__gte = 2                                 |
| regex    | 正则表达式匹配               | username_regex = r'^abc'                         |



### F对象和Q对象

```python
from django.db.models import F, Q
```

F对象可以获取属性的值, 可以实现一个模型的不同属性的运算操作

```python
conpanies = Company.objects.filter(c_boy_num__lt=F('c_girl_num'))
```

Q对象可以对查询条件进行封装，同时支持逻辑运算

```python
conpanies = Company.objects.filter(Q(c_boy_num__gt=1) & Q(c_girl_num__gt=5))
```

| 含义 | 操作符 |
| ---- | ------ |
| and  | &      |
| or   | \|     |
| not  | ~      |

### 使用原生SQL

```python
users = User.objects.raw（"select * from user"）
```



## 管理器

模型类和数据库中表对应，模型类的对象和记录对象，模型类本身没有数据库访问功能，但模型类中有一个Manager类的对象，通过管理器对象可以实现和数据库的访问。

当我们没有为模型类定义管理器时，Django会为模型类生成一个名为objects的管理器，自定义管理器后，Django不再生成默认管理器objects。

管理器是Django的模型进行数据库操作的接囗，Django应用的每个模型都拥有至少一个管理器。Django支持自定义管理器类，继承models.Manager。

自定义管理器类主要用于两种情况：

- 修改原始查询集

```python
class StudentManager(models.Manager):
    def get_queryset(self):
        return super(StudentManager, self).get_queryset().filter(is_delete=False)  
    # 重写父类方法， 只返回on_delete=False的数据
    
class Student(models.Model):
    s_name = models.CharField(max_length=16)
    s_grade = models.ForeignKey(Grade, on_delete='CASCADE')
    is_delete = models.BooleanField(default=False)
    objects = StudentManager()    
    # 使用objects查询的数据都是on_delete=False的数据
```

- 向管理器类中添加额外的方法

```python
class StudentManager(models.Manager):
    def get_queryset(self):
        return super(StudentManager, self).get_queryset().filter(is_delete=False)

    def create(self, name, grade, is_delete=False):
        student = Student()
        student.s_name= name
        student.s_grade = grade
        student.is_delete = is_delete
```

- 同一模型也可以有多个管理器

  ```python
  class Student(models.Model):
  	object1 = models.Manager()  # 原Manager，查询所有数据
  	object2 = StudentManager()  # 自定义Manager，只查询on_delete=False的数据
  ```

  

## 迁移原理

### 正向迁移

1. 生成迁移

   根据既有迁移文件和Model进行比对，生成新的迁移文件。

   ```python
   manage.py makemigrations
   ```

2. 执行迁移

   在数据库中查询执行记录，直接过滤已执行的迁移文件，执行未执行的迁移文件并记录。

   ```
   managy.py migrate
   ```

### 反向迁移

以上内容都是从Model生成SQL，django还提供了从SQL生成Model的方法。

```python
manage.py inspcetdb  # 打印Model的python语句
manage.py inspcetdb > App/models.py  # 直接生成models.py
```

生成的Model自带元信息，其中包含`manage=False`, 表示该模型不会被迁移系统管理

## 模型关系

当模型对象之间存在级联关系时，必须要设置`on_delete`属性。

| on_delete属性      | 说明                                           |
| ------------------ | ---------------------------------------------- |
| models.CASCADE     | 级联删除，删除主表字段同时删除级联数据         |
| models.PROTECT     | 保护模式， 存在级联数据时无法删除主表字段      |
| models.SET_NULL    | 置空模式，删除主表字段同时设置从表字段为null   |
| models.SET_DEFAULT | 置默认值，删除主表字段同时设置从表字段为默认值 |
| models.SET()       | 删除的时候重新动态指向一个实体                 |

### 一对一

从表中添加外键并添加唯一约束

```python
class Person(models.Model):
    p_name = models.CharField(max_length=16)
    p_sex = models.BooleanField(default=0)


class IDcard(models.Model):
    id_num = models.CharField(max_length=18)
    id_person = models.OneToOneField(Person,
        						   on_delete=models.CASCADE,
        						   null=True,
        						   blank=True)
```

Person为主表，IDcard为从表，Person和IDcard是一对一关系

- 正向查询

  ```python
  person = Person.objects.last()
  id_card = person.idcard  # 使用外键的属性 
  person_id_num = id_card.id_num
  ```

  idcard为**隐性属性**，默认表示为**从表模型名的小写**

- 反向查询

  ```python
  id_card = IDcard.objects.last()
  person = id_card.id_person  # 直接查询 显性属性
  ```

- 跨关系查询

  ```python
  person = Person.objects.get(IDcard__id_num=370209)
  id_card = IDcard.objects.get(person__p_name='mz')
  ```

  

### 一对多

一对多，是最常见的一种设计。就是 A 表的一条记录，对应 B 表的多条记录，且 A 的主键作为 B 表的外键。

直接根据外键实现，“一”指主表中的一条数据，“多”指从表中的多条数据。

在学生和班级的关系中，班级表是主表，学生表是从表，因为班级表的一条数据对应了学生表的多条数据，学生表的**外键**就是班级表的**主键**，所以在模型中**谁设置了外键谁就是从表**。

在文章和用户的关系中（用户发表文章），用户表是主表，文章表是从表，因为用户表的一条数据对应了文章表的多条数据。

> 在班级和学生的模型中，让学生主动来找班级优于让班级一个一个去找学生。

```python
class Grade(models.Model):
     g_name = models.CharField(max_length=16)
        
class Student(models.Model):
    s_name = models.CharField(max_length=16)
    s_grade = models.ForeignKey(Grade, on_delete='CASCADE')
```

- 正向查询

  根据主表查从表（根据班级查学生）

  ```python
  grade = Grade.objects.get(pk=1)
  students = grade.student_set.all()  # 使用外键的属性
  # type(grade.student_set) --> RelatedManager --> Manager子类
  ```

  student_set为**隐性属性**，默认表示为**从表模型名的小写_set**

- 反向查询

  ```python
  student = Student.objects.last()
  grade = student.s_grade
  ```

- 跨关系查询

  ```python
  # 根据
  grade = Grade.objects.get(student__s_name='小明')
  student = Student.objects.filter(s_grade__g_name='Grade 1')
  ```

  

### 多对多

会产生额外的关系表，关系表中使用多个外键实现，外键对应了原表的主键

```python
class Customer(models.Model):  # 主
    c_name = models.CharField(max_length=16)

class Goods(models.Model):  # 从
    g_name = models.CharField(max_length=16)
    g_customer = models.ManyToManyField(Customer)
```

- 正向查询（用顾客查商品）

  ```python
  customer = Customer.objects.get(pk=customer_id)  # 先选取一个顾客
  goods_list = customer.goods_set.all()  # 再用顾客查询商品  使用外键的属性
  ```

- 反向查询（用商品查顾客）

  ```python
  goods = Goods.objects.get(pk=1)  # 先选取一种商品
  customer_list= goods.g_customer.all()  # 再用商品查询所有购买过商品的顾客 显性属性
  ```

- 跨关系查询

  ```python
  # 根据顾客表用商品名查询购买过商品的顾客（顾客表中无商品相关字段）
  # 也就是 在顾客表中查顾客，但是利用了商品的信息
  customer_list = Customer.objects.filter(goods__g_name='switch')
  ```

根据商品表用顾客名查询顾客购买的商品（商品表中的顾客字段用外键连接）

也就是 在商品表中查商品，但是利用了顾客的信息

  goods_list = Goods.objects.filter(g_customer__c_name='dmz')

  


以上两种方式实际上都是生成ManyRelatedManager类，并对其进行操作。



## 模型继承

默认一个模型在数据库中映射一张表，子模型对应的表会通过外键和父表产生关联。默认继承会将通用字段放到父表中，特有字段存储在子表中，父表与字表使用外键连接。

关系型数据库中关系越复杂，查询效率越低，父类表中也会导致过多的数据存储，可以设置元信息`abstract = True` 使模型抽象，抽象的模型不会在数据库中产生表，于是子表拥有父表的字段。

```python
class Animal(models.Model)：
	a_name = 'Jack'
    class Meta：
        abstract =True
class Dog(Animal):
    d_eat = 'meat'
```

设置`abstract =True`不会产生级联表`Animal`，也不会将继承表的数据存储在级联表中



------

## AbstractUser

user继承AbstractUser

\#如果用户继承了AbstractUser，需要修改auth_user的模型

```
- settings.py
AUTH_USER_MODEL = 'User.User'
```

执行迁移最好是在系统第一次迁移之前，否则会产生冲突。

------



# Template

## 模版位置

1. Project目录下建立templates文件夹，各APP可以复用模版，在`settings.py的TEMPLATES'`下注册。
2. APP目录下建立templates文件夹，无需注册模版，但是必须注册APP。

## 模板渲染

```python
from django.shortcuts import render
render(request, templatesname, context=None)

args:
	request: 请求对象
	templatesname: 模板名称
	context: 字典，将python变量通过字典形式传入模板，然后可以通过{{var}}的模版语法调用
```



## 模板语法

### 变量

{{var}}表示变量，var即context中的key。

如果模板中引用变量未传值，则会被置为空，不会报错

------

### 内置标签

#### `csrf_token`

```html
<form method="post">
	{% csrf_token %}
</form>
```

#### `for`

```django
{% for athlete in athlete_list %}
    <li>{{ athlete.name }}</li>
{% endfor %}
```

```django
# 避免在字典中将items作为键
{% for key, value in data.items %}
    {{ key }}: {{ value }}
{% endfor %}
```

```django
{% for value in c %}
	数据存在
{% empty %}
	数据不存在
{% endfor %}
```

>  https://docs.djangoproject.com/en/3.0/ref/templates/builtins/#for

#### `if`

```django
{% if condition1 %}
    first answer
{% elif condition1 %}
    second answer
{% else %}
    other answer
{% endif %}
```

> https://docs.djangoproject.com/en/3.0/ref/templates/builtins/#if

------

#### `url`

```python
# in project urls
path('', include(('Article.urls', 'Article'), namespace='blog')) 
# in app urls
path('read/<int:article_id>/', views.read, name='read')
# 在模板中以'namespace:name'的形式动态调用url
{% url 'blog:read' article.id %}   # in template
```



------

### 过滤器

#### `add`

```django
# If value is 4, then the output will be 6.
{{ value|add:"2" }}   
```

#### `length`

```django
# If value is "abcd", the output will be 4.
{{ value|length }}  
```

#### `slice`

```django
# if value is 'abcdefg', the output will be 'abcde'
{{ some_list|slice:":5" }}   
```

#### `truncatechars`

超出指定范围的字符将被省略，同时在末尾添加省略号`...`

```django
# If value is "Joel is a slug", the output will be "Joel i…".
{{ value|truncatechars:7 }} 	
```

#### 自定义过滤器

内置过滤器功能有限，如果不能满足需求，可以自己定义过滤器。



------

### 模板继承

`{% extends %} `继承父模板

```django
——> base.html
{% block content %}
{% endblock %}

——> child.html
{% extends 'base.html' %}
{% block content %}
	在此设计子模版独有的内容
{% endblock %}
```

### Javascript获取模板

在JS中获取模板语法的值，例如获取page.number的值，需要写成以下形式

```js
var value = {{page.number}} + 0; // 和一个数值型相加
```

否则在调用时会**找不到声明的变量**

## 静态资源

### 静态资源配置

静态资源指css、js、images等需要从外部导⼊的资源。

1. 在project目录下创建static目录

2. 在settings中注册

   ```python
   # way1
   STATIC_URL = '/static/'
   STATICFILES_DIRS = [os.path.join(BASE_DIR, "static"), ]
   
   # way2 
   STATIC_ROOT = '/static/'
   # 在各app中创建static目录存放静态文件
   manage.py collectstatic  # 将各APP中的静态文件收集到根目录的static下
   ```
   
3. 在模板中使用静态资源

   ```django
   {% load static %} # 放置于模板开头
    <img src="/static/img/img.jpeg" alt=""> 		# 硬编码 绝对路径 (不推荐)
    <img src="{% static 'img/img.jpeg' %}" alt=""> # 动态编码 相对路径 ✔
   ```

注意：静态资源的HTML不支持模版语法，Django模板语法需要Django的后端支持



## 媒体文件

### 媒体文件配置

```python
# 在settings中注册MEDIA_ROOT
MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, "media")
```

```python
# 在根路由下加入路径
from django.views.static import serve
re_path(r'^media/(?P<path>.*)$', serve, {'document_root': MEDIA_ROOT})
```

```PYTHON
# 在settings的TEMPLATES下添加后可以在模板中引用{{MEDIA_URL}}
'django.template.context_processors.media'
```



### 客户端配置

- 文件上传必须使用`POST`请求方式，同时添加`{% csrf_token %}`进行验证。

- form表单上传文件需要添加`enctype='multipart/form-data'`，

```html
<!-- 例子 -->

<form action="{% url 'app:imgfield' %}" method="post" enctype="multipart/form-data">
    {% csrf_token %}
<span>用户名：</span>
<input type="text" name="username" placeholder="请输入用户名">
<br>
<span>头像：</span>
<input type="file" name="icon">
<button>上传</button>
</form>
```

### 上传文件分组保存

upload_to还可以写成特定的格式，图片就可以按照日期被分组保存

```python
icon = models.ImageField(upload_to='%Y%M/icons') 

|—— 2020 —— 1 —— icons
		|—— 2 —— icons
		|—— 3 —— icons		
```

```python
>> type(user.icon)
<class 'django.db.models.fields.files.ImageFieldFile'>

>> user.icon.url
icons/IMG_0995.JPG  # url的路径为模型中upload_to指定的位置
```

必要的配置

```python

```



## 错误视图

Django内置了处理HTTP错误的视图（django.views.defaults），主要错误及视图包括：

- 403错误：permission_denied（权限拒绝）

- 404错误：page_not_found（找不到指定文件）

- 500错误：server_error（服务器内部错误）

如果要显示错误视图，首先要关闭DEBUG模式

`DEBUG = FALSE`

同时也可以自定义错误视图，只要在templates目录下创建视图文件即可，例如405.html，当需要使用错误视图时，django会就近调用错误视图文件。





------

# Views

Django中的视图主要用来接受Web请求，并做出响应。视图的本质就是一个Python中的函数。

视图的响应分为两大类：

- 以Wson数据形式返回

- 以网页的形式返回

```markdown
URL组成
schema（协议）+  [ip:port]（域名）+  path（路径）+ QueryString（GET请求参数）+anchor（锚点）
schema：http/https
path：/app/index
QueryString：?arg1=xxx&arg2=xxx
```



## 参数捕获

```python
path('index/<arg>', views.index)  # 不指定第一个参数的类型
path('getstudents/<int:g_id>', views.get_students)  # 指定第一个参数为int类型
  # 指定第一个参数为str类型，第二个参数为int类型
path('getstudents/<str:g_name>/<int:g_id>', views.get_students)  
```



## 反向解析

### 根路由namespace配置

Django2.2中，反向解析中关于app_name有两种设置方法

1. 根路由的include(arg, namespace)的arg参数需要传入一个**元组**

   ```python
   path('app/', include(('App.urls', 'App'), namespace='app'))
   ```

   元祖中的第一个元素代表子路由，第二个元素代表app_name
   
2. 根路由的include(arg, namespace)的arg参数只传入子路由的路径

   ```python
   path('two/', include('Two.urls, namespace='second')
   ```

   但是子路由urls.py下必须显式指定app_name

   ```python
   - Two/urls.py
   app_name = 'Second'
   ```

### 子路由name配置

```
path('students/', views.students, name='stu')
```

### 模型反向解析路径配置

1. 无参数传递

   ```html
   <a href="{% url 'second:stu' %}">all students</a>
   ```

2. 有参数传递

   ```html
   <a href="{% url 'second:getdate' 2020 6 1%}">get date</a>
   ```



## HttpRequest 

服务器在接收到Http请求后，会根据报文创建HttpRequest对象视图，其中的第一个参数就是HttpRequest对象

**request对象的常用属性**

| 属性         | 说明                                                         |
| ------------ | ------------------------------------------------------------ |
| content-type | 请求的MIME类型                                               |
| GET          | QueryDict对象，包含GET请求参数                               |
| POST         | QueryDict对象，包含POST请求参数                              |
| COOKIES      | ⼀个标准的Python字典，包含所有的cookie，键和值都为字符串     |
| SESSION      | ⼀个类似于字典的对象，表示当前的会话，只有当Django启用会话的⽀持时才可⽤ |
| PATH         | ⼀个字符串，表示请求的页面的完整路径，不包含域名             |
| method       | ⼀个字符串，表示请求使用的HTTP方法，常⽤值包括：GET、POST    |
| FILES        | ⼀个类似于字典的QueryDict对象，包含所有的上传文件            |
| META         | 请求的请求头的源信息（请求头中的键值对）                     |
| encoding     | 字符编码                                                     |
| scheme       | 协议                                                         |

request.META中包含request的元信息

| 键           | 说明       |
| ------------ | ---------- |
| HTTP_REFERER | 来源页面   |
| REMOTE_ADDR  | 客户端IP   |
| REMOTE_HOST  | 客户端主机 |

常用方法

| 方法                 | 说明                    |
| -------------------- | ----------------------- |
| get_host()           | 获取主机名+端口         |
| get_full_path()      | 获取请求路径+查询字符串 |
| is_ajax()            | 如果是ajax请求返回True  |
| build_absolute_uri() | 完整的url               |



## Response

### HttpResponse

每一个视图函数必须返回一个响应对象，HttResponse对象由程序员创建并返回。

```python
from django.http import HttpResponse
```

| 属性         | 说明               |
| ------------ | ------------------ |
| content      | 字节字符串         |
| charset      | 字符编码           |
| status_code  | http状态码         |
| content_type | 指定输出的MIME类型 |

```python
def goodbye(request)：
    response = HttpResponse()
    response.content = 'good bye'
    response.charset = "utf-8"
    response.content_type= 'text/html
    return response
```



### HttpResponseRedict

```python
# way1
from django.http import  HttpResponseRedirect
HttpResponseRedirect(reverse('two:register'))
# way2
from django.shortcuts import redirect
redirect(reverse('two:register'))
```

### JsonResponse

JsonResponse是HttpResponse的子类，用于向客户端返json数据。一般用于ajax请求。它的content-type缺省值为：application/json



## 会话技术

### cookie

cookies是浏览器为Web服务器存的一小信息。每欢浏览器从某个服务器请求页面时，都会自动带上以前收到的cookie保存在客户端，安全性较差，注意不要保存敏感信息。

- cookie是一种客户端会话技术
- cookie不能跨域名使用
- cookie默认不能支持中文
- 以key-value进行存储
- 默认请求会携带本网站的所有cookie

1. 设置cookie

   ```python
   HttpResponse.set_cookie（key, value='', max_age=None）
   # max_age表示秒，也可以接受timedelta类型
   
   #同set_cookie，salt表示对cookie加密
   HttpResponse.set_signed_cookie(key, value, salt=''，max_age=None)
   ```

2. 获取cookie

   ```python
   HttpRequest.COOKIEs.get(key)
   # 获取加“盐”的cookie
   HttpRequest.get_signed_cookie(key, default=RAISE_ERROR, salt='', max_age=None)
   ```

3. 删除cookie

   ```python
   HttpResponse.delete_cookie(key)
   ```

### Session

Session基于cookie, 可以将关于用户的数据保存在服务器，在客户端cookie中加一个sessionid。

- session是一种服务端绘画技术
- django中session的数据在存储在数据库
- 默认使用base64
- 依赖于cookie

**流程**

1. 当用户来访问服务端时，服务端会生成一个随机字符串；

2. 当用户登录成功后把`{sessionID：随机字符串}`组织成键值对加到cookie里发送给用户；

3. 服务器以发送给客户端cookie中的随机字符串做键，用户信息做值，保存用户信息；

4. 再访问服务时客户端会带上sessionid，服务器根据sessionid来确认用户是否访问过网站

sessionid即为diango_session表中的session_key，session_data为加密后保存的数据

加密方式是用一个随机字符串和数据拼接成一个整体，再对这个整体用base64算法加密

1. 设置session

   ```python
   request.session['username'] = username
   ```

2. 获取session

   ```python
   username = request.session.get('username')
   ```

3. 删除session

   ```python
   ✖request.delete_cookie('sessionid')  # 删除cookie后session表中记录还在 
   ✖del request.session['username']  # 仅删除了session_data字段中的部分数据，记录还在
   ✔request.session.flush()  # flush() 清空所有并删除表中的session记录
   ```

### token

- 在web开发中，效果等同于cookie和session
- 主要应用场景是不支持cookie的移动端和专用客户端开发中

## CSRF

- 防止跨站攻击
- django中内置CSRF插件
- 模板渲染的时候，会向response中添加**csfrtoken**，在进行POST请求时，服务器会验证其有效性

```HTML
# 视图函数添加装饰器@csrf_exempt 可以免csrf验证
<form action="{% url 'two:stulogin' %}" method="post">
    {% csrf_token %}
    <spanm>用户名：</spanm>
    <input type="text" placeholder="请输入用户名" name="username">
    <button>查询</button>
</form>
```





# Cache

缓存的作用

- 提升服务器响应速度；

- 将执行过的操作数据存储下来，在一定时间内再次获取数据时，直接从缓存中获取；

- 缓存使用内存级缓存

- Django内置缓存框架

  

**缓存框架**

django内置了缓存框架，并提供了几种常用的缓存

- 基于Memcached缓存

  内存级数据库，支持的数据类型较少，性能低于redis

- 使用数据库进行缓存

- 使用文件系统进行缓存

  数据库缓存本质上也是文件系统缓存

- 使用本地内存进行缓存

  访问速度快，但不能多台计算机协同工作

- 提供缓存扩展接口
- 通过扩展使用redis缓存✔



## 缓存配置

### 数据库缓存

1. 创建缓存表

```python
manage.py createcachetable my_cache
```

2. 缓存配置

   ```python
   - settings.py
   CACHE = {'default': 
     {'BACKEND': 'django.core.cache.backends.db.DatabaseCache',  # 指定缓存使用的引擎 
      'LOCATION': 'my_cache',
      'TIMEOUT': 60 * 5,  # 缓存超时时间
      'OPTIONS': {'MAX_ENTRIES': 300,  # 最大缓存记录
                  'CULL_FREQUENCY': 3  # 缓存最大后的删除比例  1/CULL_FREQUENCY
                 }
     }
   }
   ```

**缓存使用**

```
# 在视图函数中使用
@cache_page()
o timeout 秒60*5缓存五分钟o cache 缓存配置，默认default o key_prefix前置字符串
```



### Redis缓存

1. 安装redis

2. 安装django-redis模块

```
pip install django-redis
pip install django-redis-cache
```

3. 在settings中配置

   ```python
   CACHES = {
       "default": {
           "BACKEND": "django_redis.cache.RedisCache",
           "LOCATION": "redis://127.0.0.1:6379/1",  # /1 表示数据库
           "OPTIONS": {
               "CLIENT_CLASS": "django_redis.client.DefaultClient",
               "CONNECTION_POOL_KWARGS": {"max_connections": 100}
               # "PASSWORD": "密码",
           }
       }
   }
   ```

### 多缓存

```python
CACHES = {
  'default': 
  {'BACKEND': 'django.core.cache.backends.db.DatabaseCache',  
   'LOCATION': 'my_cache',
   'TIMEOUT': 60 * 5,  # 缓存超时时间
   'OPTIONS': {'MAX_ENTRIES': 300,  # 最大缓存记录
               'CULL_FREQUENCY': 3}  # 缓存最大后的删除比例  1/CULL_FREQUENCY
  }
  "redis_backend": {
        "BACKEND": "django_redis.cache.RedisCache",
        "LOCATION": "redis://127.0.0.1:6379/1",
        "OPTIONS": {"CLIENT_CLASS": "django_redis.client.DefaultClient",
                    "CONNECTION_POOL_KWARGS": {"max_connections": 100}
                   }
         }
```



## 缓存使用

### 单级缓存

1. 装饰器实现

```python
from django.views.decorators.cache import cache_page
@cache_page(60)
def test_cache（request）：
	pass
"""
args:
    time: 缓存时间，单位为秒
	cache: 缓存配置，默认default
	key_prefix:前置字符串
"""
```

2. 手动实现

   ```python
   caghe.set(key, value, timeout)  # 设置缓存
   cache.get(key)  # 获取缓存
   ```

### 多缓存的实现

```python
# way1 手动指定缓存类型
from django.core.cache import caches
cache = caches['redis_backend']  # 指定缓存类型
result = cache.get('news')

# way2 装饰器中传入cache参数
from django.views.decorators.cache import cache_page
@cache_page(60, cache='redis_backend')
def test(request):
    pass
```





# 中间件

中间件：是一个轻量级的，底层的插件，可以介入Django的请求和响应过程（面向切面编程）

中间件的本质就是一个python类

面向切面编程（Aspect Oriented Programming）简称AOP，AOP的主要实现目的是**针对业务处理过程中的切面进行提取**，它所面对的是处理过程中的某个步骤或阶段，以获得逻辑过程中各部分之间低耦合的隔离效果。

## 流程

1.在工程目录下创建middleware目录

2.目录中创建一个python文件

3.在python文件中导入中间件的基类

```python
from diango.utils.deprecation import MiddlewareMixin
```

4.在类中根据功能需求，创建切入需求类，重写切入点方法

```python
class LearnAOR（MiddlewareMixin）：
	def processrequest（self.request）：
		print（'request的路径"，request.GET.path）
```

5.启用中间件，在settings中进行配置MIDDLEWARE

`'middleware.LearnMiddle.HelloMiddle'('文件夹名.脚本名.类名')`

6. 中间件的调用是根据注册列表顺序执行，如果没有在切点进行返回，中间件会依次执行，如果返回，之后的中间件不会执行。

## 中间件的切点类型

**1. process_request**

在执行路由前被调用，每个请求上都会调用，不主动进行返回或返回HttpResponse对象

```python
def process_request(self.request)：
	print('request的路径"，request.GET.path)
```
**2. process_view**

调用视图之前执行，每个请求都会调用，不主动进行返回或返回HttpResponse对象

```python
def process_view(self,request,view_func,view_args,view_kwargs):
    """
    :param request: HttpRequest对象
    :param view_func: 是⼀个即将调⽤的视图函数，不是字符串函数名
    :param view_args: 传递给视图函数的位置参数
    :param view_kwargs: 传递给视图函数的关键字参数
    """
	pass
```

**3. process_template_response** 

在视图刚好执行完后进行调用，只要视图返回一个render方法返回的对象，就会调用process_template_response，不生动进行返回或返回HttpResponse对象

**4. process_response**

所有响应返回浏览器之前调用，每个请求都会调用，返回HttpResponse对象

**5. process_exeption**

当视图抛出异常时调用，返回None或返回HttpResponse对象

```python
def process_exception(self, request, expection):
    print(request, expection)
    return HttpResponseRedirect(reverse('app:hello'))
```

# 分页

django提供了分页的工具，存在于django.core中

## Paginarot

Paginator：数据分页工具

对象创建：`paginator = Paginator(queryset，per_page)`

| Paginator属性 | 说明              |
| ------------- | ----------------- |
| count         | 对象总数          |
| num_pages     | 页面总数          |
| page_range    | 页码列表，从1开始 |
|               |                   |

| Paginator方法 | 说明             |
| ------------- | ---------------- |
| page          | 获得一个page对象 |

## Page

Page：具体的某一页面

```
page = Paginator.page(number)
```

| Page对象属性 | 说明                        |
| ------------ | --------------------------- |
| object_list  | 当前页面上所有的数据对象    |
| number       | 当前页的页码值              |
| paginator    | 当前page关联的Paginator对象 |

| Page对象方法           | 说明                   |
| ---------------------- | ---------------------- |
| has_next()             | 判断是否有下一页       |
| has_previous():        | 判断是否有上一页       |
| has_other_pages()      | 判断是否有上一页       |
| next_page_number():    | 返回下一页的页码       |
| previous_page_number() | 返回上一页的页码       |
| len()                  | 返回当前页的数据的个数 |

| Paginator对象属性 | 说明                                 |
| ----------------- | ------------------------------------ |
| per_page          | 每页显示条目数量                     |
| count             | 数据总个数                           |
| num_pages         | 总页数                               |
| page_range:       | 总页数的索引范围，如: (1,10),(1,200) |
| page              | page对象                             |
|                   |                                      |

```python
def get_students(request):    
    student = Student.objects.all()
    page = int(request.GET.get('page', 1))
    per_page = int(request.GET.get('per_page', 10))
    paginator = Paginator(student, per_page)  # generate paginator object
    page_object = paginator.page(page)  # generate page object
    data = {'page_object': page_object}
    return render(request, 'student_with_page.html', context=data)
```



------



# RESTful

到底什么是 RESTful 架构？

1. 每一个URI代表一种资源

2. 客户端和服务器之间，传递这种资源的某种表现层

3. 客户端通过四个HTTP动词，对服务端资源进行操作，实现“表现层状态转换”



##  REST构架

在APP中，将views.py转换成python package，结构化视图函数目录，可以进行更有效的管理。

```markdown
views ———— __init__.py  # 部署index
	 |———— studentAPI.py  # 部署student相关API
	 |———— schoolAPI.py  # 部署school相关API
```

同时，在`__init__.py`中导入

```python
from .studentAPI import *
from .schoolAPI import *
```

##  类视图

基于类的视图（Class-based views）提供了另一种将视图实现为Python对象而不是函数的方法。它们不替换基于函数的视图，但与基于函数的视图相比具有一定的差异和优势，类视图是一种具有REST风格的视图。

> 这里的CBV是纯Django实现，后面会介绍基于RESTful Framework的CBV

### 创建类视图

```python
- views.py

from django.views import View
class HelloCBV(View):
    def get(self, request, uid):
        pass
    def post(self, request):
        pass
```

### 路由注册

as_view是类方法，只能通过类调用，不能通过对象调用，它是类视图的入口点。

```python
- urls.py

path('hello/<int:uid>/', views.HelloCBV.as_view(), name='hello'),
```



### 类视图的类型

Django 提供了数量众多的类视图，但是相关的文档并不完善，想要彻底了解某一类视图的功能必须查看其源码，由于封装程度很高，使用时也必须按照Django规定的方式，所以不利于团队开发使用。

- 基本视图：view、TemplateView、RedirectView

- 通用显示视图：DetailView、ListView

- 通用编辑视图：FormView、CreateView、Updateview、DeleteView

- 通用日期视图：ArchivelndexView、YearArchveView、MonthArchiveView、WeekArchiveView

  ​						   DayArchiveView、TodayArchiveView、DateDetaiView

- 基于类的视图mixins

  - 简单的mixins：ContextMixi\TemplateResponseMixin
- 单个对象mixins：SingleobjectMixin、SingleObjectTemplateResponseMixin
  
  - 多个对象混合：MultipleobjectMixin、MultipleObjectTemplateResponseMixin

**TemplateView**

```python
> using TemplateView
# way1 在路由的as_view中赋值
path('template', views.HelloTemplateView.as_view(template_name ='hello.html'), name='template')

# way2 在类中赋值
class HelloTemplateView(TemplateView):
	template_name ='hello.html'
```



### 在类视图中使用装饰器

在类视图中使用为函数视图准备的装饰器时，不能直接添加装饰器，需要使用method_decorator将其转换为适用于类视图方法的装饰器。

```python
# way 1 对类视图中所有函数使用装饰器
@method_decorator(my_decorator, name='dispatch')
class PersonView(View)
	def get(self, request):
		pass
	def post(self, request):
		pass
    
# way2 对类视图中特定函数使用装饰器    
@method_decorator(my_decorator, name='post')
class PersonView(View)
	def get(self, request):
		pass
	def post(self, request):
		pass
    
# way 3 对类视图中部分函数使用装饰器
class PersonView(View)
	@method_decorator(my_decorator)
	def get(self, request):
		pass
    @method_decorator(my_decorator)
	def post(self, request):
		pass
    def put(self, request):  # 没有对put方法使用装饰器
        pass
```

- - -

------

# RESTful Framework

##  QuickStart

```python
pip install djangorestframework
managy.startapp HelloRESTful
```

1. 在app目录下创建serializers目录，然后创建序列化模型

```python
from django.contrib.auth.models import User
from rest_framework import serializers

class UserSerializer(serializers.HyperlinkedModelSerializer):
    class Meta:
        model = User  # User为Django自带模型
        fields = ('url', 'username', 'email', 'is_staff')
```

2. 在views中创建类视图

```python
from django.contrib.auth.models import User
from rest_framework import viewsets
from HelloRESTful.serializers import UserSerializer

# 如果继承的是View，则为基本类视图，是需要自己写get、post之类的方法
class UserViewSet(viewsets.ModelViewSet):
    serializer_class = UserSerializer
    queryset = User.objects.all().order_by('-date_joined')
```

3. 在子路由下配置router

```python
from rest_framework import routers
from HelloRESTful import views

router = routers.DefaultRouter()
router.register(r'user', views.UserViewSet)
```

4. 在根路由下配置url

```python
path('restful/', include(router.urls))
```

>  DjangoRESTful与系统耦合的内容较多，router的url最好配置在根路由下

5. 在settings中注册

```python
INSTALLED_APPS = [
	......
	'rest_framework', 
	......
]
```



##  Serializer

Serializer解决的主要是数据的序列化问题，如果当前要返回一个object有以下属性

```
p_name = '张三'
p_age = 30
```

可以手动创建一个字典`{'p_name': '张三', 'p_age': 30}`，但如果字段很多，手动创建的效率是非常低下的，Serializer帮助解决了数据的序列化问题。

首先创建基本模型

```python
class Person(models.Model):
    p_name = models.CharField(max_length=16)
    p_age = models.IntegerField(default=1)
    p_sex = models.BooleanField(default=False)
```

Django-RESTful有以下几种常用的序列化器：

### Serializer

```python
- serializes.py

class PersonSerializer(serializers.Serializer):

    id = serializers.IntegerField(read_only=True)
    p_name = serializers.CharField(max_length=16)
    p_age = serializers.IntegerField(default=1)
    p_sex = serializers.BooleanField(default=False)
    
	# 必须设置的抽象方法
    def create(self, validated_data):
        return Person.objects.create(**validated_data)

    def update(self, instance, validated_data):
        # 实例对象的属性通过get得到，如果没有得到就设置为原始值
        instance.p_name = validated_data.get('p_name', instance.p_name)
        instance.p_age = validated_data.get('p_age', instance.p_age)
        instance.p_sex = validated_data.get('p_sex', instance.p_sex)
        instance.save()
        return instance
```

```python
- views.py

class PersonView(View)
	def get(self, request):
        person = Person.objects.all()		
        # many=True表示对多条数据序列化
        person_serializer = PersonSerializer(instance=person, many=True)
        return JsonResponse(person_serializer.data, safe=False)
	def post(self, request):
        p_name = request.POST.get('p_name')
        p_age = request.POST.get('p_age')
        person = Person()
        person.p_name = p_name
        person.p_age = p_age
        person.save()
        person_serializer = PersonSerializer(instance=person)  # 将实例序列化
        return JsonResponse(person_serializer.data)
```



### ModelSerializer

`serializers.Serializer`通过精确指定字段可以实现数据的序列化，但代码量比较大；`serializers.ModelSerializer`可以简化指定字段的步骤，能够让你自动创建一个具有模型中相应字段的Serializer类，同时默认简单实现了create方法和update方法。

```python
- serializes.py

class PersonSerializer(serializers.ModelSerializer):
    class Meta:
        model = Person
        # 可以将fields属性设置成'__all__'来表明使用模型中的所有字段。
        fields = ('p_name', 'p_age', 'p_sex')
```



### HyperlinkedModelSerializer

用法和`ModelSerializer`基本相似，不通电是可以在fields中指定`url`字段

```python
fields = ('url', 'p_name', 'p_age', 'p_sex')
```



##  Request and Response

### Request

REST框架引入了一个扩展了常规HttpRequest的Request对象，并提供了更灵活的请求解析。REST框架的request的类型为`<class 'rest_framework.request.Request'>`，将Django中的request作为自己的属性，`request._request`表示`<class 'django.core.handlers.wsgi.WSGIRequest'>`。

> 使用RESTFUL Framework的Request需要在创建类时继承基于类的视图，例如APIView，或创建函数时使用装饰器@api_view

Request对象的核心功能是request.data属性，它与request.POST类似，但是兼容了更多请求方法。

`request.POST`只能获取'POST'方法的参数;（等价于request._request.POST）

`request.data`兼容了'POST'、'PUT'、'PATCH'方法，可以获取以上方法的参数。

| 属性                     | 说明                                         |
| ------------------------ | -------------------------------------------- |
| content_type             | MIME类型                                     |
| query_params             | GET请求的参数                                |
| stream                   | 数据流                                       |
| data                     | POST、PUT、PATCH方法参数                     |
| user                     | 在请求上获取用户，相当于在请求上添加一个属性 |
| auth                     | 认证，在请求上附加属性，属性值为token        |
| successful_authenticator | 认证成功                                     |

### Response

REST框架还引入了一个Response对象，这是一种获取未渲染（unrendered）内容的TemplateResponse类型，并使用内容协商来确定返回给客户端的正确内容类型。

```python
from rest_framework.response import Response
```

在创建Response之前需要将数据进行序列化，传递的必须是未渲染的数据。

```python
book_serializer = BookSerializer(data=request.data)  # 将数据序列化
if book_serializer.is_valid():
    return Response(book_serializer.data, status=201)  # 渲染成客户端请求的内容类型
```



### Status codes

纯数字的状态码并不是很容易被理解，所以REST框架为status模块中的每个状态代码提供更明确的标识符。使用它们来代替纯数字的HTTP状态码是个很好的主意。

```python
from rest_framework import status
```



### wrapping

REST框架提供了两个可用于编写API视图的包装器（wrappers）

- **基于FBA的@api_view装饰器**

  添加装饰器，同时手动指定允许的请求方法`@api_view(http_method_names=['GET', 'POST'])`，**视图函数**可以使用RESTFUL framwork定义的属性和方法。

  ```python
  from rest_framework.decorators import api_view
  
  @api_view(http_method_names=['GET', 'POST'])
  def books(request):  
      # 被装饰过的视图函数的request成为<class 'rest_framework.request.Request'>
      if request.method == 'GET':
          return Response(data={'msg': 'ok'})
      elif request.method == 'POST':
          book_serializer = BookSerializer(data=request.data)
          if book_serializer.is_valid():
              book_serializer.save()
              return Response(book_serializer.data)
          else:
              return Response(data={'msg': 'error'},
                              status=status.HTTP_400_BAD_REQUEST)
  ```

- **基于CBA的APIView类**

  通过继承APIView，**视图类**可以使用RESTFUL framwork定义的属性和方法。
  
  ```python
  from rest_framework.views import APIView
  
  class AnimalView(APIView):
      def get(self, request):
          pass
      def post(self, request):
          pass
  
  ```



##  Class-based View

Django RESTful CBV提供了比原生CBV更为强大的功能

### APIView

```python
from rest_framework.views import APIView

class AnimalView(APIView):
    def get(self, request):
        animal = Animal.objects.all()
        # 对于QuerySet需要添加many=True
        animal_serializer = AnimalSerializer(instance=animal, many=True)
        data = {'msg': 'ok',
                'data': animal_serializer.data}
        return Response(data=data, status=status.HTTP_200_OK)
    
    def post(self, request):
        animal_serializer = AnimalSerializer(data=request.data)
        if animal_serializer.is_valid():
            animal_serializer.save()
            return Response(animal_serializer.data)
        else:
            return Response(data={'msg': 'error'},
                            status=status.HTTP_400_BAD_REQUEST)
```

> RESTful Framework提供了一系列可继承的视图类，总体来说，ViewSet的封装程度较高，代码可读性较低，需要开发者对源码有深入的理解；相对而言，继承APIView则是对Django原生CBV一定程度上拓展，代码可读性较高，所以继承APIView是一种较为方便的选择。

### Mixins

```python
- views.py
class GamesView(ListCreateAPIView):
    serializer_class = GameSerializer
    queryset = Game.objects.all()

class GameView(RetrieveUpdateDestroyAPIView):
    serializer_class = GameSerializer
    queryset = Game.objects.all()
```

```python
- urls.py

urlpatterns = [
    path('games/', views.GamesView.as_view()),
    path('games/<int:pk>/', views.GameView.as_view(), name='game-detail'),
]
```



## ViewSet & Routers

对于继承`ViewSet`的类，可以使用router路由方式

```python
- views.py
class GameModelViewSet(ModelViewSet):
    serializer_class = GameSerializer
    queryset = Game.objects.all()
```



```python
- sub - urls.py
router = DefaultRouter()
router.register(r'game', GamelodelViewSet)
```

```python
- project - urls.py
path('mixin/', include(router.urls))
```

## 实例

```python
class Register(APIView):
    def get(self, request):
        return render(request, 'register.html')
    @csrf_exempt
    def post(self, request):
        user_serializer = UserSerializer(data=request.data)
        if user_serializer.is_valid():
            user_serializer.save()
            return Response(user_serializer.data)
        else:
            print(user_serializer)
            data = {'msg': 'error', 'status': 404}
            return Response(data=data, status=status.HTTP_400_BAD_REQUEST)
```



# Celery

- 消息队列
  - 异步任务
  - 定时任务

`__all__`：可被外界访问的属性

使用Django ORM存储异步信息

## 流程

```python
django-admin startproject Project
manage.py startapp Celery
```

1. 安装celery相关模块

```powershell
pip install -U celery  # 安装celery
pip install -U celery[redis]  # 安装支持redis依赖项 
pip install django-celery-results  # 支持Django ORM/Cache作为后端储存result
```

2. 创建`project/celery.py`来保存celery的配置信息

```python
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'project.settings')
app = Celery('project')
app.config_from_object('django.conf:settings', namespace='CELERY')
app.autodiscover_tasks()

@app.task(bind=True)
def debug_task(self):
    print('Request: {0!r}'.format(self.request))
```

3. 在`project/__init__.py`中配置

```python
from .celery import app as celery_app

__all__ = ('celery_app',)
```

4. 在`project/settings.py中`配置

```python
INSTALLED_APPS = [
	......
	'diango_celery_results', 
	......
]

CELERY_BROKER_URL ='redis://localhost:6379/1'  # 消息容器URL
CELERY_TASK_SERIALIZER = 'json'
CELERY_ACCEPT_CONTENT = ['json']
- opitional
  - CELERY_RESULT_BACKEND = 'django-db'  # 结果存储到django数据库
  - CELERY_RESULT_BACKEND = 'django-cache'  # 结果存储到django缓存
```

5. 迁移

   ```python
   manage.py migrate
   ```

6. 创建`tasks.py`并配置视图函数

   ```python
   - Celery/tasks.py
   
   from time import sleep
   from celery import shared_task
   
   @shared_task
   def add(x, y):
       print('疯狂计算中')
       sleep(3)
       return x + y
   
   - Celery/views
   
   from Celery.tasks import add
   from django.http import HttpResponse
   
   def hello(request):
       result = add.delay(1, 4)
       return HttpResponse(result)
   ```

7. 启动消息队列

   ```python
   # -A指定了celery对象的位置, -l指定woker的日志级别 -c表示并发度
   celery -A proj worker -l info  # -l 表示 --loglevel
   ```

## 发送邮件实例

```python
- Mail/tasks.py

from django.core.mail import send_mail
def send_email(receive):
    subject = 'Subject'
    message = 'message'
    from_address = 'superkuangzero@163.com'
    to_address = ['512090083@qq.com'] 
    send_mail(subject, message, from_address, to_address)
```

```python
- Project/settings.py
# 163邮箱
EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
EMAIL_HOST_USER="superkuangzero@163.com"
EMAIL_HOST_PASSWORD = 'PLZJKJNSRRLCGGJD'  # 授权码
EMAIL_HOST = "smtp.qq.com"
EMAIL_PORT = 465/994  # TLS-->25
# 以下两项只能有一个为True
EMAIL_USE_SSL = True
EMAIL_USE_TLS = False

```

## BUG

celery在win10上存在bug，运行`celery -A proj worker -l info`会报错，解决方案如下：

```python
# way1
pip install eventlet 
celery -A proj worker -l info -P eventlet

# way2
- Project/celery.py
os.environ.setdefault('FORKED_BY_MULTIPROCESSING', '1')
```

> 参考文献  https://github.com/celery/celery/issues/4081



# Django Admin

## QuickStart

1. 创建超级用户

   ```python
   manage.py createsuperuser  
   ```

2. 注册模型

   ```python
   - App/admin.py
   
   admin.site.register(Model)
   ```

## 自定义显示条目

```python
class UserAdmin(admin.ModelAdmin):
    def sex(self):
        return '女' if self.sex else '男'
    list_display = ['username', 'age', sex]

admin.site.register(User, UserAdmin)
```

## 分组显示field

## 关联操作

## 定制站点信息

```python
class ArticleAdmin:
list display= ['title', 'click num', 'love_num, 'user']
search_fields= ['user', 'date']
list_editable = ['click_num', 'love_num']
list_filter = ['date']  # 过滤器
```

```
- __init__
default_app_config = 'Article.apps.ArticleConfig'
- apps
class UserConfig（AppConfig）：
    name = 'Article'
    verbose_name = '文章操作'
```

```python
from xadmin import views

class BaseSettings:
    enable_themes =True
    use_bootswatch=True
class GlobalSettings:
    site_title = '博客后台管理系统'
    site_footer = '👴的博客'
xadmin.site.register(views.BaseAdminView, BaseSettings)
xadmin.site.register(views.CommAdminView, GlobalSettings)
```





------

## 第三方admin

### xadmin

1. 下载xadmin并安装依赖项

    ```
    git clone https://github.com/sshwsfc/xadmin
    cd D:\ProjectFiles\Repositories\xadmin
    pip install -r requirements.txt
    ```

xadmin并没有适配django3，所以需要手动修改配置文件

> https://www.cnblogs.com/wtil/p/12363875.html

2. 注册APP

   ```
   INSTALLED_APPS = [
       ......
       'xadmin', 
       'crispy_forms',
       ......
   ]
   ```

3. 将xadmin文件下复制到项目下，配置路由

    ```python
    path('xadmin/', xadmin.site.urls)
    ```

4. 在App中创建`adminx.py`

   ```python
   import xadmin
   # 如果模型继承了AbstractUser则无需注册
   xadmin.site.register(Article, ArticleAdmin)  # 注册APP
   ```
   
   



### ckeditor

1. 安装ckeditor

```python
pip install django-ckeditor 
```

2. 在settings中注册以及配置

```python
INSTALLED_APPS = [
    ...
    
    'ckeditor',
    'ckeditor_uploader'  # 如果需要文件上传

    ...
]

CKEDITOR_UPLOAD_PATH = "uploads/"  # 这里依赖于MIEIA_ROOT路径
```

3. 在模型中使用富文本

```python
content = RichTextField(verbose_name='正文')
```



# 

# 原理

## RESPONSE

```python
HttpResponseBase
__init__方法  (self, content_type=None, status=None, reason=None, charset=None)
reason_phrase方法
charset方法
serialize_headers方法
_content_type_for_repr方法
_convert_to_charset方法
与cookie相关的方法

HttpResponse -- HttpResponseBase
继承父类的__init__方法
重写serialize方法
content方法
```

slug:主要用于生成一个URL路径, Django中的Slug会为每条model记录，自动（也可以手动）的根据当前记录的每个字段的Value生成一个URL路径



## View

### View

- 定义`as_view`方法（入口）

  - 传入的参数不可以和`http_method_names`中的方法同名
  - as_view无法传入View中没有的属性
  - 定义`view`方法
    - 如果有get方法，没有head方法，则视为get方法
    - 如果没有request则报错
    - 返回时调用dispatch方法
  - 返回`view`方法对象  
    - `as_view`的返回值view是一个函数对象，所以urls中的`views.HelloCBV.as_view()`是一个函数，这样就保持了格式与之前的路由一致。
    - view方法调用时返回值为调用dispatch方法

- 定义`dispatch`方法

  - 如果请求方法在允许的列表中，使handler获取request.method.lower()请求方法，

    例如HTTP请求为GET，则handler为get方法对象

  - 不存在则返回http_method_not_allowed

  - 返回为调用handler，即调用HTTP请求方法（调用get方法、post方法等）

- 定义`set_up`方法

- 定义`http_method_not_allowed`方法

- 定义`options`方法

  ![](http://store.secretcamp.cn/uPic/View%E5%BA%95%E5%B1%822021053114452616224435264E0Vwl4E0Vwl.png)
  
  

### TemplateView

  

  - TemplateResponseMixin
    - 将内容渲染到模版中
    - template_name  指定模板
    - template_engine 指定模板引擎
    - response_class (TemplateResponse)
    - content_type
    - render_to_response方法
  - ContextMixin
    - 接受上下文
    - 从视图函数传递到模板的内容
- View
    - 分发（服务器来啥请求就调用对应请求的函数）

  

  ### ListView

  - MultipleObjectTemplateResponseMixin
    - TemplateResponseMixin
  - BaseListView
  - MultipleObjectMixin
    - View

  ### DetailView

  - SingleObjectTemplateResponseMixin
    - 继承TemplateResponseMixin
      - template_name属性：指定模板的名字
      - render_to_response方法：用上下文内容生成Response
    - 添加了template_name_field, template_name_suffix两个属性
  - BaseDetailView
    - 继承SingleObjectMixin（用于检索单目标）
      - 继承ContextMixin（获取模板的上下文内容）
      - 定义get_object方法（查询object信息，返回object）
        - 调用get_queryset方法（获取查询集）
      - 定义get_queryset方法
      - 定义get_context_object_name方法
      - 定义get_context_data方法
        - 调用get_context_object_name方法
    - 继承View：dispatch分发
    - 定义get了方法（入口）
      - 调用get_object
      - 调用get_context_data
      - 将context_data转变成Response

### FormView

```
class RegisterForm(ModelForm):
    class Meta:
        model = User
        fields = '__all__'
        exclude = ['first_name', 'date_joined', 'last_name']

    def clean_username(self):
        """
        数据验证
        """
        username = self.cleaned_data.get('username')
        result = User.objects.filter(username=username)
        if result.first():
            raise ValidationError('该用户名已经存在')
        print('用户名验证成功！')
        return username
```

