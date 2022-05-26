
# CRUD
### a) Vapaaehtoinen: CRUD. Tee alusta lähtien Django-ohjelma, jossa voit luoda (C), lukea (R), muokata (U) ja poistaa (D) tietueita. Keksi sille joku aihe, vaikka kalavale / tall tales. Tässä ohjelmassa ei tarvitse autentikoida käyttäjiä. Tästä tehtävästä ei tarvitse tehdä yksityiskohtaista raporttia, palauta ohjelman lähdekoodi, README.md ja ruutukaappaus.

Sources used in this report:
Tero Karvinen himself, in the course: https://terokarvinen.com/2021/python-web-service-from-idea-to-production-2022/

Karvinen 2022: [Django 4 Instant Customer Database Tutorial](https://terokarvinen.com/2022/django-instant-crm-tutorial/)

Mozilla Developer Network contributors 2022: Django Web Framework (Python): [Django Tutorial Part 3: Using models](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Django/Models)

Django contributors 2022: Django 3.2 documenation: [Generic display views](https://docs.djangoproject.com/en/3.2/ref/class-based-views/generic-display/) (DetailView, ListView)

Refresh Oxford 2022: [Classy Class-Based Views](http://ccbv.co.uk/projects/Django/3.2/) (CreateView, ListView, DetailView, UpdateView, DeleteView)

Django contributors 2022: Django 3.2 documenation: [Generic editing views](https://docs.djangoproject.com/en/3.2/ref/class-based-views/generic-editing/) (CreateView, UpdateView, DeleteView)

`Used Ubuntu Mate 21.10, Lenovo Thinkpad E15`

```
sicki@Parasite:~$ lsb_release -a
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 21.10
Release:	21.10
Codename:	impish
```
**Steps taken to get project and app:**
```console
sicki@Parasite:~$ mkdir django-site
sicki@Parasite:~$ cd django-site
sicki@Parasite:~/django-site$ virtualenv --system-site-packages -p python3 env/
created virtual environment CPython3.9.7.final.0-64 in 106ms
  creator CPython3Posix(dest=/home/sicki/django-site/env, clear=False, no_vcs_ignore=False, global=True)
  seeder FromAppData(download=False, pip=bundle, setuptools=bundle, wheel=bundle, via=copy, app_data_dir=/home/sicki/.local/share/virtualenv)
    added seed packages: pip==20.3.4, pkg_resources==0.0.0, setuptools==44.1.1, wheel==0.34.2
  activators BashActivator,CShellActivator,FishActivator,PowerShellActivator,PythonActivator,XonshActivator
sicki@Parasite:~/django-site$ source env/bin/activate
(env) sicki@Parasite:~/django-site$ which pip
/home/sicki/django-site/env/bin/pip
(env) sicki@Parasite:~/django-site$ touch requirements.txt
(env) sicki@Parasite:~/django-site$ echo django==3.2 > requirements.txt
(env) sicki@Parasite:~/django-site$ cat requirements.txt
django==3.2
(env) sicki@Parasite:~/django-site$ pip install -r requirements.txt
Collecting django==3.2
  Using cached Django-3.2-py3-none-any.whl (7.9 MB)
Collecting asgiref<4,>=3.3.2
  Using cached asgiref-3.5.2-py3-none-any.whl (22 kB)
Collecting pytz
  Using cached pytz-2022.1-py2.py3-none-any.whl (503 kB)
Collecting sqlparse>=0.2.2
  Using cached sqlparse-0.4.2-py3-none-any.whl (42 kB)
Installing collected packages: sqlparse, pytz, asgiref, django
Successfully installed asgiref-3.5.2 django-3.2 pytz-2022.1 sqlparse-0.4.2
(env) sicki@Parasite:~/django-site$ django-admin startproject anthalo
(env) sicki@Parasite:~/django-site$ ./manage.py createsuperuser
(env) sicki@Parasite:~/django-site$ cd anthalo
(env) sicki@Parasite:~/django-site/anthalo$ ./manage.py createsuperuser
(env) sicki@Parasite:~/django-site/anthalo$ ./manage.py makemigrations
No changes detected
(env) sicki@Parasite:~/django-site/anthalo$ ./manage.py migrate
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying admin.0003_logentry_add_action_flag_choices... OK
  Applying contenttypes.0002_remove_content_type_name... OK
  Applying auth.0002_alter_permission_name_max_length... OK
  Applying auth.0003_alter_user_email_max_length... OK
  Applying auth.0004_alter_user_username_opts... OK
  Applying auth.0005_alter_user_last_login_null... OK
  Applying auth.0006_require_contenttypes_0002... OK
  Applying auth.0007_alter_validators_add_error_messages... OK
  Applying auth.0008_alter_user_username_max_length... OK
  Applying auth.0009_alter_user_last_name_max_length... OK
  Applying auth.0010_alter_group_name_max_length... OK
  Applying auth.0011_update_proxy_permissions... OK
  Applying auth.0012_alter_user_first_name_max_length... OK
  Applying sessions.0001_initial... OK
(env) sicki@Parasite:~/django-site/anthalo$ ./manage.py createsuperuser
Username (leave blank to use 'sicki'): admin
Email address: admin@anthalo.com
Password: 
Password (again): 
Superuser created successfully.
(env) sicki@Parasite:~/django-site/anthalo$ ./manage.py startapp crud
(env) sicki@Parasite:~/django-site/anthalo$ micro anthalo/settings.py #ADDED CRUD TO INSTALLED_APPS
```
**Edit `admin.py`**
```console
(env) sicki@Parasite:~/django-site/anthalo$ micro crud/admin.py
```
```python
#admin.py
from django.contrib import admin
from . import models #import models

admin.site.register(models.Crud) #register
```
# New Stuff - Making necessary fields
First edited `models.py`
```console
(env) sicki@Parasite:~/django-site/anthalo$ micro crud/models.py
```python
#models.py
from django.db import models

class Crud(models.Model):
    name = models.CharField(max_length=300) # name
    def __str__(self):

        return self.name	

    name = models.CharField(max_length=300) # writer
    joke = models.TextField(max_length=300, default="") # joke
    origin = models.IntegerField(default=1900) #origin year
                
    def get_absolute_url(self):
	    return f"/{self.pk}"
```
Edited `views.py`
```python
#views.py
from django.views.generic import ListView, DetailView, CreateView, UpdateView, DeleteView #imported necessary Views
from . import models

class CrudListView(ListView):
    model = models.Crud

class CrudDetailView(DetailView):
    model = models.Crud
        
class CrudCreateView(CreateView):
    model = models.Crud
    fields = "__all__"
    success_url = "/"
         
class CrudUpdateView(UpdateView):
    model = models.Crud
    fields = ['name','joke'] #only edit name and the joke
    success_url = "/"  
      
class CrudUpdateYearView(UpdateView):
    model = models.Crud
    fields = ['origin'] #enable editing origin year in detailed view 

class CrudDeleteView(DeleteView):
    model = models.Crud
    succes_url = "/" 
```    
**edited `urls.py`**
`(env) sicki@Parasite:~/django-site/anthalo$ micro anthalo/urls.py`
```python
#urls.py
from django.contrib import admin
from django.urls import path
from crud import views #import views from crud

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', views.CrudListView.as_view()), #default to root
    path('<int:pk>', views.CrudDetailView.as_view()), #added path for detailed view
    path('new/', views.CrudCreateView.as_view()), #added path for "new"
    path('<int:pk>/update', views.CrudUpdateView.as_view()), #added path for "update"
    path('<int:pk>/update_year', views.CrudUpdateYearView.as_view()), #added path for "update year"
    path('<int:pk>/delete', views.CrudDeleteView.as_view()), #added path for "delete"
]
```
Now i ran a test to see if everything "would" work, as there are more steps to do, but just a side check.
```console
(env) sicki@Parasite:~/django-site/anthalo$ micro crud/models.py
(env) sicki@Parasite:~/django-site/anthalo$ micro crud/admin.py
(env) sicki@Parasite:~/django-site/anthalo$ micro anthalo/urls.py
(env) sicki@Parasite:~/django-site/anthalo$ micro crud/views.py
(env) sicki@Parasite:~/django-site/anthalo$ ./manage.py makemigrations
Migrations for 'crud':
  crud/migrations/0001_initial.py
    - Create model Crud
(env) sicki@Parasite:~/django-site/anthalo$ ./manage.py migrate
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, crud, sessions
Running migrations:
  Applying crud.0001_initial... OK
  ```
**Now i needed to do few .html:s so that nothing points to a page that doesnt exist and gives errors.**

First make directory for `templates`and make/edit a file for `list`
```console
(env) sicki@Parasite:~/django-site/anthalo$ mkdir -p crud/templates/crud/
(env) sicki@Parasite:~/django-site/anthalo$ micro crud/templates/crud/crud_list.html
```
```html
<html>
<head>
<title>Jokes</title>
</head>
<body>
	<h1>Tell a joke!</h1>
	<p>Jokes from previous visitors:</p>
<!--- make an object list. Lists the movie, year and genre --->

{% for joke in object_list %}
    <li>
    <b>Writer:</b> {{ joke.name }}<br><br>
    <b><i></b>{{ joke.joke }}</i><br><br>
	<a href="{{ joke.get_absolute_url }}">Details</a>
	<a href="{{ joke.get_absolute_url }}/update">Update</a>
	<a href="{{ joke.get_absolute_url }}/delete">Delete</a>
	<br><br>
	</li> 
{% endfor %}

<!-- Everything else is just to make it look nice and personal testings-->
<p>
	<a href="new/">Tell your own</a> <!-- add a button for "new" -->
</p>

<i>Refreshed at: <span id="date"</span></i></p>
	
<script>
var time = new Date();
document.getElementById("date").innerHTML = time;
</script>

</body>
```
Ran server to see if frontpage is showing:
```
(env) sicki@Parasite:~/django-site/anthalo$ ./manage.py runserver
```
Page for `/new` was missing, but link was working Ok.
Now i needed to make a .html for `form`
```
(env) sicki@Parasite:~/django-site/anthalo$ micro crud/templates/crud/crud_form.html
```
```html
Specs:
<form method=post>
	{% csrf_token %}
	{{ form.as_p }}
	<input type=submit>
</form>
<br> <a href="/">Back</a><br> 
```
Ran server again,. `new`and `update` was working!
Next i needed a page, where `delete`leads to.
As the debug-page told, i needed to make `crud_confirm_delete.html`
```
(env) sicki@Parasite:~/django-site/anthalo$ micro crud/templates/crud/crud_confirm_delete.html
```
```html
<form method=post>
	{% csrf_token %}
	<p><b>Are you sure you want to remove this joke?</b></p>
	<input type=submit value=Delete>
</form>
<br> <a href="/">Back</a><br> 
```
Ran server, everything is working.

[App here](https://github.com/therealhalonen/django_test_jokes)

