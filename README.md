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