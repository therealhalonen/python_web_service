# pw1 Hello DJ A
## Setting up Django 3.2, creating simple CRM and testing & adding stuff to it.
Used [Ubuntu Mate 22.04 LTS](https://ubuntu-mate.org/download/amd64/) in VirtualBox.
```
halonen@halonen-VirtualBox:~$ lsb_release -a
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 22.04 LTS
Release:	22.04
Codename:	jammy
```
**a) Asenna Django-kehitysympäristö.**

Installed Django Python web framework, and made a simple CRM with these instructions:
[Django 4 Instant Customer Database Tutorial](https://terokarvinen.com/2022/django-instant-crm-tutorial/) by Tero Karvinen, replacing the version with 3.2 by adding it to requirements:
```
halonen@halonen-VirtualBox:~/django$ cat requirements.txt
django==3.2
```
**b) Lisää omia kenttiä malliin.**

After all was set up, i started to add stuff using recommended `micro`

```
(env) halonen@halonen-VirtualBox:~/django/halo$ micro crm/models.py
```
```python
from django.db import models

class Customer(models.Model):
    name = models.CharField(max_length=300) #name field
    def __str__(self):	# define a name for customer

        return self.name	# inherit(?) name from the field

    city = models.CharField(max_length=15, default="Ei mistään kotosin") # city field  
    age = models.FloatField(default=18) # age field, default is 18 for newly added customer
    info = models.TextField(default=("Description")) # customers info field
    created_at = models.DateTimeField(auto_now_add=True) # not working
    modified_at = models.DateTimeField(auto_now=True) # not working
```
After a lot of trials and errors, forgetting `makemigrations` and `migrate` , i got it working beside the created and modified at dates...
Checked few sites, but didnt really get it:

[Djangoproject](https://docs.djangoproject.com/en/4.0/topics/db/examples/many_to_one/)

[Stack overflow](https://stackoverflow.com/questions/56310322/django-datetimefield-with-auto-now-add-asks-for-default) 

Everything else running smooth:

**c) Tee lisää käyttäjiä, jotka saavat kirjautua Djangon adminiin**

Added another user, gave Staff and Superuser status
![Image 1](/pw1/res/adduser.png)

![Image 2](/pw1/res/added_user.png)

![Image 3](/pw1/res//give_rights.png)

![Image 4](/pw1/res/loggable_admins.png)

Added few customers:

![Image 5](/pw1/res/customers.png)

![Image 6](/pw1/res/customer_fields.png)

**edited: report will be updated later, to more detailed explanation about setting the Django enviroment.**
