## Creating a Project
1. Start project:
```
    django-admin startproject <projectname>
```

<!-- /TOC -->

## Creating Apps
2. Create apps to project: 
```
    python manage.py startapp <appname>
```

3. Include the app in the list of ```INSTALLED_APPS``` located 
in the file ```<project-name>/settings.py```:
```
    #...
    INSTALLED_APPS = [
        "appname.apps.AppnameConfig"
        #...]
    #...
```
<!-- /TOC -->

## Creating Views
4. Create view function in ```<appname>/views.py``` to handle the requests:
```
from django.shortcuts import render

# Create your views here.

def homepage(request):
    return render(request, "<appname>/home.html", {})
```
5. Create a HTML template at ```<appname>/templates/<appname>/``` to be rendered by the previously created view function.
   
6. Add a route at the to your app in the file ```<project-name>/urls.py```:
```
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('pages/',include("pages.urls")),
]
```
<!-- /TOC -->

## Create Models for database:

1. At ```models.py``` define your models, see an example below:

``` 
class DataModel(models.Model):
    name = models.Charfield(max_length=50)
```

2. Make migrations with the models:

```
>>> python manage.py makemigrations <appname>
```

3. Finally migrate:

```
>>> python manage.py migrate
```

## Serving Static Files

1. Make sure that ```django.contrib.staticfiles``` is included in your ```INSTALLED_APPS```.

2. In your settings file, define ```STATIC_URL```, for example:
```
    STATIC_URL = "static/"
```
3. In your templates, use the static template tag to build the URL for the given relative path using the configured staticfiles STORAGES alias.
```
    {% load static %}
    <img src="{% static 'my_app/example.jpg' %}" alt="My image">
```
4. Store your static files in a folder called static in your app. For example ```my_app/static/my_app/example.jpg```.

