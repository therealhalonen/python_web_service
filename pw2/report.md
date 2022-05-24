
# ListView
### Tee alusta lähtien uusi Django-projekti. Tee siihen sivu, joka listaa tietueita tietokannasta ilman kirjautumista. Valitse jokin muu aihe kuin aiemman esimerkin CRM. Aivan simppeli esimerkkiprojekti riittää, mutta valitse sille jokin esimerkkiaihe.
Sources used in this report:

Tero Karvinen himself, in the course: https://terokarvinen.com/2021/python-web-service-from-idea-to-production-2022/

Tero Karvinen, Django CRM tutorial: https://terokarvinen.com/2022/django-instant-crm-tutorial/

Mozilla, # Django Tutorial Part 3: Using models: https://developer.mozilla.org/en-US/docs/Learn/Server-side/Django/Models


`Used Ubuntu Mate 21.10, Lenovo Thinkpad E15`

```
sicki@Parasite:~$ lsb_release -a
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 21.10
Release:	21.10
Codename:	impish
```
Started making a new Django-project from scratch with same method as before. All steps included here, because the previous report was a little short. **(to be fixed later)**
This is a database for rentable movies.
**Started with installing virtualenv and/or check that i had the newest version:**
```
sicki@Parasite:~$ sudo apt-get -y install virtualenv
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
virtualenv is already the newest version (20.4.6+ds-2)..
sicki@Parasite:~$ mkdir testSite
sicki@Parasite:~$ cd testSite
```
**Did a setup for virtualenv:**
```
sicki@Parasite:~/testSite$ virtualenv --system-site-packages -p python3 env/
created virtual environment CPython3.9.7.final.0-64 in 117ms
  creator CPython3Posix(dest=/home/sicki/testSite/env, clear=False, no_vcs_ignore=False, global=True)
  seeder FromAppData(download=False, pip=bundle, setuptools=bundle, wheel=bundle, via=copy, app_data_dir=/home/sicki/.local/share/virtualenv)
    added seed packages: Django==3.2, asgiref==3.5.2, pip==20.3.4, pkg_resources==0.0.0, pytz==2022.1, setuptools==44.1.1, sqlparse==0.4.2, wheel==0.34.2
  activators BashActivator,CShellActivator,FishActivator,PowerShellActivator,PythonActivator,XonshActivator
```
**Activated virtualenv**
```
sicki@Parasite:~/testSite$ source env/bin/activate
```
**Checked that pip was in the right place**
```
(env) sicki@Parasite:~/testSite$ which pip
/home/sicki/testSite/env/bin/pip
```
**Requirements had been manually changed to Django version 3.2, and installed with `pip install -r requirements.txt`** 
**Checked that everything is ok with them:**
```
(env) sicki@Parasite:~/testSite$ cat requirements.txt
django==3.2
(env) sicki@Parasite:~/testSite$ django-admin --version
3.2
```
**Started the project called movies, moved to the right directory and maked first-time migrations**
```
(env) sicki@Parasite:~/testSite$ django-admin startproject movies
(env) sicki@Parasite:~/testSite$ cd movies
(env) sicki@Parasite:~/testSite/movies$ ./manage.py makemigrations
No changes detected
(env) sicki@Parasite:~/testSite/movies$ ./manage.py migrate
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, rental, sessions
Running migrations:
  No migrations to apply.
```
**Then created superuser, and filled the needed fields it asked:**
```
(env) sicki@Parasite:~/testSite/movies$ ./manage.py createsuperuser
```
**Started an app named rental**
```
(env) sicki@Parasite:~/testSite/movies$ ./manage.py startapp rental
```
**Edited settings.py and added the rental app to `installed apps`**
```
(env) sicki@Parasite:~/testSite/movies$ micro movies/settings.py
```
```python
#settings.py
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'rental', #added rental-app
]
```
**Edited `models.py`, gave a movie the fields for genre, made and short description** 
```
(env) sicki@Parasite:~/testSite/movies$ micro rental/models.py
```
```python
#models.py
from django.db import models

class Movie(models.Model):
    name = models.CharField(max_length=300) # movie name
    def __str__(self):

        return self.name	
    genre = models.CharField(max_length=300) # movie genre
    year_made = models.IntegerField(default=1900) # movie year
    description = models.TextField(default='short description of the movie', max_length=300) # short description
```
**Edited `admin.py`**
```
(env) sicki@Parasite:~/testSite/movies$ micro rental/admin.py
```
```python
#admin.py
from django.contrib import admin
from . import models

admin.site.register(models.Movie)
```
Now i had a database for movies ready.
**Next i ran it:**
```
(env) sicki@Parasite:~/testSite/movies$ ./manage.py makemigrations
(env) sicki@Parasite:~/testSite/movies$ ./manage.py migrate
(env) sicki@Parasite:~/testSite/movies$ ./manage.py runserver
```
```
Django version 3.2, using settings 'movies.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```
**Everything was working!**

## New stuff = ListView
Now that i have everything set up, i want to make a site that lists the movies from the database.
**First i edited `urls.py`:**
```
(env) sicki@Parasite:~/testSite/movies$ micro movies/urls.py
```
and added MovieListView, and pointed it to root path, so that when the site loads, it loads by default.
**Also imported views from rental:**
```python
#urls.py
from django.contrib import admin
from django.urls import path
from rental import views #added import for views from rental

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', views.MovieListView.as_view()) #added this
]
```
**Now i needed to add stuff to `views.py`:**
```
(env) sicki@Parasite:~/testSite/movies$ micro rental/views.py`
```
```python
#views.py
from django.views.generic import ListView
from . import models

class MovieListView(ListView):
    model = models.Movie
```
**And lastly i made a .html, for movie list. This will be the page showing in the front:**
```(env) sicki@Parasite:~/testSite/movies$ cd rental
(env) sicki@Parasite:~/testSite/movies/rental$ mkdir templates
(env) sicki@Parasite:~/testSite/movies/rental$ cd templates
(env) sicki@Parasite:~/testSite/movies/rental/templates$ mkdir rental
(env) sicki@Parasite:~/testSite/movies/rental/templates$ cd rental
(env) sicki@Parasite:~/testSite/movies/rental/templates/rental$ micro movie_list.html
```
**Added a basic HTML code with a little JS, just for testing purposes, critical code for making the list is commented:**
```html
<!-- movies_list.html-->
<html>
<head>
</head>
<body>
	<h1>Movie list:</h1>
	<p>Movies available for rental:</p>
<!--- Starting from here: make an object list. Lists the movie, year and genre... --->
{% for movie in object_list %}
    <li>{{ movie.name }}, Year: {{ movie.year_made }}, Genre: {{ movie.genre }}</li> 
{% endfor %}
<!-- ...and ending to here. Everything else is just to make it look nice and for personal testings-->
	<p><i>Refreshed at: <span id="date"</span></i></p>
	
<script>
var time = new Date();
document.getElementById("date").innerHTML = time;
</script>

</body>
```
**Now i moved back to `/testSite/movies/`, made migrations and migrated. Then ran the server, to see if everything was working:**
```
(env) sicki@Parasite:~/testSite/movies$ ./manage.py makemigrations
No changes detected
(env) sicki@Parasite:~/testSite/movies$ ./manage.py migrate
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, rental, sessions
Running migrations:
  No migrations to apply.
(env) sicki@Parasite:~/testSite/movies$ ./manage.py runserver
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).
May 24, 2022 - 14:44:12
Django version 3.2, using settings 'movies.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```
**Smoothly ran locally. I went to the admin panel first and added some movies:**
![Image 1](/pw2/res/movie_list_admin.png)
![Image 2](/pw2/res/movie_detail.png)
**After adding them and stuff to the fields, i wanted to check the front page to see if everything was in order:**
![Image 3](/pw2/res/final_listView.png)

**Painless**, everything seemed like supposed to!
Total time was ~2h, including reporting.


