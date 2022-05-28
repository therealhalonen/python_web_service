# MyBooks
**a) Oma kirjatietokanta. Tee alusta lähtien kirjatietokanta Djangolla. Siinä pitää olla ylläpitoliittymä salasanan takana. Ja admin-panelin ulkopuolella liittymä, jossa voi luoda, lukea, muokata ja poistaa kirjoja (CRUD). Tässä a-kohdassa admin-panelin ulkopuolella ei tarvitse olla kirjautumista. Tästä tehtävästä ei tarvitse tehdä yksityiskohtaista raporttia, palauta ohjelman lähdekoodi, README.md ja ruutukaappaus. Palautus heti kun on valmista, viimeistään su 20:00.**

**b) Vapaaehtoinen, vaikeahko: laita muokkaussivut (CUD) salasanan taakse. (LoginRequiredMixin)**

**c) Vapaaehtoinen, vaikeampi: laita käyttäjille mahdollisuus rekiströityä. (CreateView, User, UserForm)**

*This report was optional, and will probably contain **errors**. These are just for my **personal use** later.
Also comments on the codes are for my personal notes for learning and remembering stuff*

Noob-friendly source for login-page and authentication i used, are/were:
[Putkis-Dude: Commit #1](https://github.com/PutkisDude/simple-django/commit/fd2d57b31af1684c5f1990fe2676c51ed47ccc6b)
[Putkis-Dude: Commit #2](https://github.com/PutkisDude/simple-django/commit/d487116c9427135f94b8c15d17faf496e3512a20)
**Thanks for sharing!** 
Login and registering a user:
[LearnDjango - Login and Logout Tutorial](https://learndjango.com/tutorials/django-login-and-logout-tutorial)
[Crispy Forms](https://ordinarycoders.com/blog/article/render-a-django-form-with-bootstrap)
[CSEstack.org](https://www.csestack.org/django-sign-up-registration-form/)
[techwithTim](https://www.csestack.org/django-sign-up-registration-form/)

So i started to make a new django-project from scratch, the same way as previously: [pw3](https://github.com/therealhalonen/python_web_service/blob/master/pw3/report.md)
This time:
Whole site: myProject
Project: library
App: book

After setting everything up to the point where i have a simple CRUD app ready and have fields for books i made a template .html to be used everywhere.
```console
(env) sicki@Salvage-820-Linux:~/myProject/library$ micro book/templates/book/base.html
```
```html
<!doctype html>
<html lang=en>
	<head>
		<title>Library</title>
		<meta charset="UTF-8">
		<style>html{
		color: white;
		background-color: DimGray 
		}
		</style>
		<meta name="vieport" content="width=device-width, initial-scale=1">
	</head>
	<body>
		{% block content %}
		{% endblock content %}
	<br>
	{% block button %}
	<button onclick="window.location.href='/';">Back</button>
	{% endblock button %}
	</body>
</html>
```
Then i started to add stuff to get a login and registering for users to work.
First i needed urls where login and logout leads to:
```console
(env) sicki@Salvage-820-Linux:~/myProject/library$ micro library/settings.py
```python
#settings.py
LOGIN_REDIRECT_URL = "/"
LOGOUT_REDIRECT_URL = "/"
```
Then edited `urls.py`
```console
(env) sicki@Salvage-820-Linux:~/myProject/library$ micro library/urls.py
```
```python
#urls.py
from django.contrib import admin
from django.urls import path, include
from book import views
#authentication
from django.contrib.auth import views as auth_views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', views.BookListView.as_view()),
    path('<int:pk>/', views.BookDetailView.as_view()),
    path('new/', views.BookCreateView.as_view()),
    path('<int:pk>/update', views.BookUpdateView.as_view()),
    path('<int:pk>/delete', views.BookDeleteView.as_view()),
#for login and logout
    path('accounts/', include('django.contrib.auth.urls')),
#for registering
    path("register/", views.register, name="register"),
]
```
Then added a .html for login, also while there added stuff to be able to use a template for all pages and change the view according to if the user is logged in or not.
```console
(env) sicki@Salvage-820-Linux:~/myProject/library$ micro book/templates/registration/register.html
```
```html
{% extends "book/base.html" %}

{% block content %}

{% if user.is_authenticated %}
<h1><b>You are already logged in as <i>"{{ user.username }}</i></b></h1>
{% else %}
<h1>Login:</h1>
<hr/>
<form method="post" action="{% url 'login' %}">
{% csrf_token %}
<table>
	<tr>
    	<td>{{ form.username.label_tag }}</td>
    	<td>{{ form.username }}</td>
	</tr>
	<tr>
    	<td>{{ form.password.label_tag }}</td>
    	<td>{{ form.password }}</td>
	</tr>
</table>

<input type="submit" value="Confirm">
</form>
{% endif %}
{% endblock content %}
```
Then edited `views.py`
```console
(env) sicki@Salvage-820-Linux:~/myProject/library$ micro book/views.py
```
```python
from django.views.generic import ListView, DetailView, CreateView, UpdateView, DeleteView
from . import models
#authentication start
from django.urls import reverse_lazy
from django.contrib.auth.mixins import LoginRequiredMixin
#authentication end
#registration start
from django.shortcuts import render, redirect
from .forms import RegisterForm
#registration end
#authentication start here     
class BookCreateView(LoginRequiredMixin, CreateView):
    model = models.Book
    fields = "__all__"
    success_url = "/"

    def form_valid(self, form):
        form.instance.user = self.request.user
        return super().form_valid(form)	
#authentication end here         

class BookUpdateView(LoginRequiredMixin, UpdateView):
    model = models.Book
    fields = "__all__"
    success_url = "/"  

    def form_valid(self, form):
        form.instance.user = self.request.user
        return super().form_valid(form)	
        
class BookDeleteView(LoginRequiredMixin, DeleteView):
    model = models.Book
    success_url = "/"

    def form_valid(self, form):
        form.instance.user = self.request.user
        return super().form_valid(form)
#registration start
def register(response):
    if response.method == "POST":
        form = RegisterForm(response.POST)
        if form.is_valid():
            form.save()

        return redirect("/")
    else:
        form = RegisterForm()
        
    return render(response, "registration/register.html", {"form":form})

class BookListView(ListView):
    model = models.Book

class BookDetailView(DetailView):
    model = models.Book
#registration end    
```

Then i edited the front page to show/hide stuff according to if user is logged in or not:
```console
(env) sicki@Salvage-820-Linux:~/myProject/library$ micro book/templates/book/book_list.html
```
```html
{% extends "book/base.html" %}

{% block content %}
{% if user.is_authenticated %}
<p>Welcome: <i>{{ user.username }}</i>!</p>
{% else %}
<p>Welcome <i>quest</i>!</p>
{% endif %}

<h1>Book collection:</h1>
<hr/>
{% for book in object_list %}
Title: <b>{{ book.title }}</b> - <b>{{ book.origin_year }}</b><br>
Author: <b>{{ book.author}}</b>

<p>
	<button onclick="window.location.href='{{ book.get_absolute_url }}';">Details</button>
	{% if user.is_authenticated %}
	<button onclick="window.location.href='{{ book.get_absolute_url }}/update';">Edit</button>
	<button onclick="window.location.href='{{ book.get_absolute_url }}/delete';">Delete</button>
	{% endif %}
	<br><br><br>	
	{% endfor %}
</p>
{% if user.is_authenticated %}
<p
	><button onclick="window.location.href='/new';">Add a book to collection</button>
</p>
<p>
	<a href="{% url 'logout' %}">Log Out</a>
</p>
{% else %}
<p>
	To be able to add your own favorite book, please 
	<a href="{% url 'login' %}">log In</a><br>
	Or if you dont have an account yet, please 
	<a href="{% url 'register' %}">register</a>
</p>
{% endif %}
{% endblock content %}
{% block button %}
{% endblock button %}
```
**This is where things started to get tricky for me!**
I needed to make a `forms.py` to get all the necessary fields for registering a user, as a default django-form only gives `username` and `password` (Correct me if im wrong....)
```console
(env) sicki@Salvage-820-Linux:~/myProject/library$ micro book/forms.py
```
```python
#forms.py
from django import forms
from django.contrib.auth.forms import UserCreationForm
from django.contrib.auth.models import User

class RegisterForm(UserCreationForm):
    email = forms.EmailField()

    class Meta:
    	model = User
    	fields = ["username", "email", "password1", "password2"]
```
I didnt get the registering page to show pretty much anything smart, so i learned that i probably needed a `django-crispy forms` 
I proceeded by adding it straight to requirements:
```console
(env) sicki@Salvage-820-Linux:~/myProject$ echo django-crispy-forms >> requirements.txt
(env) sicki@Salvage-820-Linux:~/myProject$ pip install -r requirements.txt
```
Then it needed to be added  to installed apps:
```console
(env) sicki@Salvage-820-Linux:~/myProject/library$ micro library/settings.py
```
```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'book', #my app
    'crispy_forms', #added django-crispy-forms
]
```
This is a beta-version of the registering page (because it doesnt pass the validator without errors, and also i want to add correct links to when registering is success etc):
```console
(env) sicki@Salvage-820-Linux:~/myProject/library$ micro book/templates/registration/register.html
```
```html
{% extends "book/base.html" %}

{% load crispy_forms_tags %}

{% block content %}
<p>This page is still a <b>beta</b>-version!</p>
<h1>Register:</h1>
<hr/>
<form method=post>
	{% csrf_token %}
	{{ form|crispy }}
	<input type=submit value="Confirm">
</form>
{% endblock %}
```
Its working Ok though.


