# Renting a machine from cloud
*As a first timer, the assignment took longer then expected, so did a quick report, ~24h late.*

**d) Tee Djangon tuotantoasennus niin pitkälle kuin osaat.**


**Tero Karvinen himself and some awesome co-students**, from the course: https://terokarvinen.com/2021/python-web-service-from-idea-to-production-2022/

Tero Karvinen: [Deploy Django](https://terokarvinen.com/2022/deploy-django/)


`Used Ubuntu Mate 21.10, Lenovo Thinkpad E15`

```console
sicki@Parasite:~$ lsb_release -a
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 21.10
Release:	21.10
Codename:	impish
```


These are my personal notes, not an official guide to do anything.
May contain errors! Proceed with caution, but happy you are here. - Halonen

**a) Vuokraa kone pilvestä. Sellainen kokonainen Linux-kone, virtual private server. Ota siihen ssh-yhteys.**

I rented a VPS from [Digital Ocean](https://www.digitalocean.com/) as there was a good deal via Github Education Pack.
Took the cheapest one with minimal to none goodies:
First:
- Created Droplet
- Picked up a Distro. Used recommended Debian 11.
- Only basic cheap stuff
- Generated a new supersick hard password.
- Remembered the password

**c) Vuokraa tai lainaa domain nimi. Laita se osoittamaan omaan koneeseesi.**
When i got everything set up, i went to [Name.com](name.com/) to get a domain name, as also part of Github Education Pack.
Free -> .rocks!! It rocks!

At our lecture, there was a good guide but different service provider, and as im still new to this,
i want to [duckduckgo](https://duckduckgo.com/) to find a [guide video](https://www.youtube.com/watch?v=xIaEU60JwBo)

Then everything was set up and my site that didnt contain anything was up and running, kind of.

**b) Laita vuokrakoneellesi weppipalvelin ja korvaa testisivu. Kokeile esim. kännykällä, että kone näkyy Internetiin.**

**Then to all the good stuff!**
Im familiar with ssh and linux so i went straight to my terminal:
```console
ssh root@hostip
sudo apt-get update
sudo apt-get dist-upgrade
reboot
ssh root@hostip
```
Then did the firewall setups:
```console
sudo apt-get install ufw #install ufw
sudo ufw allow 22/tcp #make hole for ssh
sudo ufw status #check
sudo ufw enable #make it work
```
Checked logs, just for fun:
```console
less /var/log/auth.log
```
No one was knocking, atleast yet.
Added a user:
```console
sudo adduser #username
```
Added the newly created user to sudo group
```console
sudo adduser #username
```
Checked in another tab that im able to login and use sudo:
```console
ssh #username@hostip
```
All good, then locked the root user:
```console
sudo usermod --lock root
```
**Reboot**

Installed Apache2 and stuff:
```console
sudo apt-get install apache2
sudo ufw allow 80/tcp
sudo systemctl restart apache2
```
```console
sicki@expedite:~$ echo "Test to see if working" |sudo tee  /var/www/html/index.html
```
All good!


**Django production install**
d) Tee Djangon tuotantoasennus niin pitkälle kuin osaat.
As connected to server with shh did testings:
```console
echo "Kukkuu" |sudo tee  /var/www/html/index.html
```
```console
cd
mkdir -p wholesite/project/static
echo "Some message fpr static" | tee wholesite/project/static/index.html
```

*Note to self: Apache name based virtual host*

Configured the config thing
```console
sudoedit /etc/apache2/sites-available/MYOWN.conf
```

```html
<!-- jobList -->
<VirtualHost *:80>
	Alias /static/ /home/username/wholesite/project/static/
	<Directory /home/username/wholesite/project/static/>
		Require all granted
	</Directory>
</VirtualHost>
```
Enabled new conf and disabled the default one:
```console
sudo a2ensite jobList.conf
sudo a2dissite 000-default.conf 
```
Tested to see if config was ok:
```console
/sbin/apache2ctl configtest
```
Gives some errors always, didnt mind. All good.

Restarted apache2
```console
sudo systemctl restart apache2
```
Check:
```console
curl http://localhost/static/
```
Working.

Then installed virtualenv:
```console
cd
cd wholesite/
sudo apt-get -y install virtualenv
virtualenv -p python3 --system-site-packages env
```
Activated virtualenv
```console
source env/bin/activate
```
Checked pip thing
```console
which pip
/home/username/wholesite/env/bin/pip
```
Edited requirements:
```console
micro requirements.txt
django==3.2 #recommended one
```
Installed requirements
```console
$ pip install -r requirements.txt
```

**Mod_wsgi stuff**
Followed a [guide from Tero](https://terokarvinen.com/2022/deploy-django/):
```console
sudoedit /etc/apache2/sites-available/PROJECT.conf
```
Super noob-friendly thing from Tero:
Replaced the stuff inside the file and changed my paths and user:

```html
Define TDIR /home/username/wholesite/project
Define TWSGI /home/username/wholesite/project/wsgi.py
Define TUSER username
Define TVENV /home/username/wholesite/env/lib/python3.9/site-packages
# See https://terokarvinen.com/2022/deploy-django/

<VirtualHost *:80>
        Alias /static/ ${TDIR}/static/
        <Directory ${TDIR}/static/>
                Require all granted
        </Directory>

        WSGIDaemonProcess ${TUSER} user=${TUSER} group=${TUSER} threads=5 python-path="${TDIR}:${TVENV}"
        WSGIScriptAlias / ${TWSGI}
        <Directory ${TDIR}>
             WSGIProcessGroup ${TUSER}
             WSGIApplicationGroup %{GLOBAL}
             WSGIScriptReloading On
             <Files wsgi.py>
                Require all granted
             </Files>
        </Directory>

</VirtualHost>

Undefine TDIR
Undefine TWSGI
Undefine TUSER
Undefine TVENV
```
*note to self: use realpath command to find paths*

Tested again:
``` console
sudo systemctl restart apache2
```
Checked if libs are installed and they were not, so:
```console
sudo-apt-get install libapache2-mod-wsgi-py3
```
Did more tests:
```console
/sbin/apache2ctl configtest
sudo systemctl restart apache2
```
*note to self curl -sI localhost*
 
Wow, that was some things ive never done before, and probably missed steps...
But went back to my local machine
Edited `settings.py`from my Django project
```python
#settings.py
DEBUG = FALSE
ALLOWED_HOSTS = ["PRODUCTION_HOST"]
```
Started to add my local stuff to my VPS, from my local wholesite/localproject folder:
```console
scp -r manage.py /localproject /localapp username@hostip:/home/username/wholesite/project/
```
Did a LOT of trial and error on the server side with database, some permissions, and settings but eventually got it working!

Final product: [anttihalonen.rocks](http://anttihalonen.rocks/) 

**Sources will be released later**

Personal note:
To reload, use:
```console
touch PROJECT/wsgi.py
``**Sources will be released later
**
