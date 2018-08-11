# Django Cheat Sheet

## manage.py 

- django-admin startproject myproject
- django-admin startapp myapp
- python manage.py createsuperuser
- python manage.py runserver
- python manage.py shell
- python manage.py test
- python manage.py makemigrations
- python manage.py migrate

## Views

### Basic view

#### app/views.py
```python
from django.http import HttpResponse

def index(request):
    return HttpResponse("Hello, world!")
```

#### app/urls.py

```python
from django.urls import path

from . import views

urlpatterns = [
    path('', views.index, name='index'),
]
```

#### mysite/urls.py

```python
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path('app/', include('app.urls')),
    path('admin/', admin.site.urls),
]
```

### Protecting views

```python
from django.contrib.auth.decorators import login_required

@login_required
def new_topic(request, pk):
    # ...
```

## Models

### Basics

```python
from django.db import models

class Tag(models.Model):
    tag_name = models.CharField(max_length=20)
```

####  Migrations

- `python manage.py makemigrations`
- `python manage.py migrate`

#### Create and Save an Object

```python
>>> tag = Tag(tag_name="python")
>>> tag.save
>>> tag.save()
```

#### Accesing an Object Fields

```python
>>> tag.id
1
>>> tag.tag_name
'python'
```

#### Update an Object Value

```python
>>> tag.tag_name = "django"
>>> tag.save()
>>> tag.tag_name
'django'
```

#### Delete an Object

```python
tag.delete()
```

### Model Manager

You can access it via the Python attribute `objects`.

#### objects.create

```python
>>> new_tag = Tag.objects.create(tag_name="python")
>>> new_tag.id
2
>>> new_tag.tag_name
'python'
```

#### objects.all

```python
>>> Tag.objects.all()
<QuerySet [<Tag: Tag object>, <Tag: Tag object>]>
```

And if we add a `__str__` Method to the model:

```python
from django.db import models

class Tag(models.Model):
    tag_name = models.CharField(max_length=20)
    
def __str__(self):
        return self.tag_name
```

We obtain:

```python
>>> Tag.objects.all()
<QuerySet [<Tag: django>, <Tag: python>]>
```

#### Iterating Over the Objects

```python
>>> tag_list = Tag.objects.all()
>>> for tag in tag_list:
...     print(tag.tag_name)
...
django
python
```

#### objects.get

```python
>>> tag = Tag.objects.get(id=1)
>>> tag.tag_name
'django'
```

#### objects.filter

```python
>>> Tag.objects.filter(id=1)
<QuerySet [<Tag: python>]>
```

### Many-to-many Relationship

An Article has multiple Tags objects, and a Tag can be used in multiple Articles.

#### Example Setup

```python
class Tag(models.Model):
    tag_name = models.CharField(max_length=20, unique=True)

    def __str__(self):
        return self.tag_name
        
class Article(models.Model):
    title = models.CharField(max_length=120, unique=True)
    tags = models.ManyToManyField(Tag)  # Asociation Table with Tag Model

    def __str__(self):
        return self.title
```

Importing Models

```python
>>> from models import Article, Tag
>>> tag_1 = Tag.objects.create(tag_name="python")
>>> tag_2 = Tag.objects.create(tag_name="django")
>>> article = Article.objects.create(title="My Article")
```

#### Associating Articles and Tags 

```python
>>> # add a Tag to an Article
>>> article.tags.add(tag_1)
>>> article.tags.add(tag_2)
>>> # or article.tags.add(tag_1, tag_2)

>>> # add an Article to a Tag
>>> tag = Tag(tag_name="flask")
>>> tag.save()
>>> tag.article_set.add(article)
```

#### Accesing Association Objects

```python
>>> article.tags.all()  # Tags in an Article
<QuerySet [<Tag: python>, <Tag: django>]>
>>>
>>> tag_1.article_set.all()  # Articles that have a Tag
<QuerySet [<Article: My Article>]>
```

#### Queries

```python
>>> Article.objects.filter(tags__id=1)
<QuerySet [<Article: First Article>]>
>>> Article.objects.filter(tags__pk=1)
<QuerySet [<Article: First Article>]>
>>> Article.objects.filter(tags=1)
<QuerySet [<Article: First Article>]>

>>> Article.objects.filter(tags__tag_name__startswith="django")
<QuerySet [<Article: First Article>]>
>>> Article.objects.filter(tags__tag_name__startswith="django").distinct()
<QuerySet [<Article: First Article>]>
>>> # distinct() eliminates duplicate rows from the query results.

>>> Article.objects.filter(tags__tag_name__startswith="django").count()
1

>>> Article.objects.filter(tags__in=[1])
<QuerySet [<Article: First Article>]>
>>> Article.objects.filter(tags__in=[tag])
<QuerySet [<Article: First Article>]>
```

#### Reverse Queries

```python
>>> Tag.objects.filter(article__id=1)
<QuerySet [<Tag: python>, <Tag: django>]>
>>> Tag.objects.filter(article__pk=1)
<QuerySet [<Tag: python>, <Tag: django>]>
>>> Tag.objects.filter(article=1)
<QuerySet [<Tag: python>, <Tag: django>]>

>>> Tag.objects.filter(article__title__startswith="My")
<QuerySet [<Tag: django>, <Tag: python>]>

>>> Tag.objects.filter(article__in=[1,2])
<QuerySet [<Tag: python>, <Tag: django>]>
>>> Tag.objects.filter(article__in=[article])
<QuerySet [<Tag: python>, <Tag: django>]>
```

#### Removing Associations

```python
>>> # remove a Tag from an Article
>>> article.tags.remove(tag)
>>> 
>>> # remove an Article from a Tag
>>> tag.article_set.remove(article)
```

## Tests

### Status code
```python
from django.test import TestCase
from django.urls import reverse

class MyView(TestCase):
    def test_index_view(self):
        url = reverse('index')
        response = self.client.get(url)
        self.assertEquals(response.status_code, 200)
```

### Resolve

```python
from django.test import TestCase
from django.urls import resolve

class MyView(TestCase):
    def test_index_url_resolves(self):
            view = resolve('/')
            self.assertEquals(view.func, index)
```