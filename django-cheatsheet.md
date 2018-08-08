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

```python
from django.http import HttpResponse

def index(request):
    return HttpResponse("Hello, world. You're at the polls index.")
```

### Protecting views

```python
from django.contrib.auth.decorators import login_required

@login_required
def new_topic(request, pk):
    # ...
```