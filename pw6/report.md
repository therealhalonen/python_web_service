# Simple Tasklist App

Made to a small, few employee locksmith company, to help them handle their daily/weekly tasks.

Easily customisable and adjustable.

Made with [Django Python Framework](https://www.djangoproject.com/)
In the course: https://terokarvinen.com/2021/python-web-service-from-idea-to-production-2022/

Current Version: Beta
- Fully functional, but plenty of features and polishing can be done with flexible timeframe, and minimal programming skills.

** 01.06.2022 - This app was developed in around 24h, with about 7days knowledge of Django and Python **

Screenshot:


Link:

Download source:

Installation instructions:

```console

$ git clone https://github.com/therealhalonen/äppi
$ git äppi
$ sudo apt-get -y install virtualenv
$ virtualenv --system-site-packages -p python3 env/
$ source env/bin/activate
$ pip install -r requirements.txt
```
Now you have to create a Django Secret Key in your preferred terminal!
```console
python3 -c 'from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())'
```
Copy the output, and add it to the `settings.py`
```
```python
# SECURITY WARNING: keep the secret key used in production secret!
SECRET_KEY = 'YOUR KEY HERE'
```
Browse back to the project folder that has manage.py
```console
$ ./manage.py runserver
```
Go to http://127.0.0.1:8000/ with your browser. firefox preferred

Profit!

Notes:
*Users are registered as inactives and marked active from admin panel.*
To create a superuser, please use official guide: [Django Project, createsuperuser.](https://docs.djangoproject.com/en/4.0/ref/django-admin/#django-admin-createsuperuser)

The feature may be turned off from `models.py` by commenting all lines after:
```console
#new user is create as inactive
```
But remember, after that anyone can register,delete, update and add content! Admin panel stays restricted to admin only.



settings.py kamat pois ennen pushia

sourcet github

Tsekkaa css serverin puolelta, etusivulle otsikko, tekijä, linkki sourceen


