## Create a Project
 1. Start project:
```
    django-admin startproject <projectname>
```

<!-- /TOC -->

## Create Apps
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






