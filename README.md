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

## Tests

### Status code
```python
from django.test import TestCase
from django.urls import reverse

class BlogView(TestCase):
    def test_index_view(self):
        url = reverse('index')
        response = self.client.get(url)
        self.assertEquals(response.status_code, 200)
```

### Resolve

```python
from django.urls import resolve

def test_index_url_resolves(self):
        view = resolve('/')
        self.assertEquals(view.func, index)
```