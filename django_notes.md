1) Start project:
'''
    django-admin startproject <project-name>
'''
 
2) Add apps to project: 
'''
    python manage.py startapp appname
'''

3) Include the app in the list of *INSTALLED_APPS* located 
in the file *<project-name>/settings.py* :
'''
    #...
    INSTALLED_APPS = [
        "appname.apps.AppnameConfig"
        ...
'''
