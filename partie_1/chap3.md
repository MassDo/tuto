# Chapitre 3 - Test d'une page d'accueil

On a terminé notre chapitre précédent sur un TF (qui échoue), pour vérifier que notre page d'accueil contienne "To-Do" dans son titre. 

Commencons a implémenter du code avec la démarche TDD ! 

On commence par ajouter une application "lists" à notre projet "superlists"
```bash
$ ./manage.py startapp lists
```
Django va créer un dossier lists (une application) contenant des fichiers dont un directement utile pour nous; test.py
Votre arborescence doit maintenant ressembler a celle-ci:
```
.
├── db.sqlite3
├── functional_tests.py
├── geckodriver.log
├── lists
│   ├── admin.py
│   ├── apps.py
│   ├── __init__.py
│   ├── migrations
│   │   └── __init__.py
│   ├── models.py
│   ├── tests.py
│   └── views.py
├── manage.py
├── Pipfile
├── Pipfile.lock
└── superlists
    ├── __init__.py
    ├── settings.py
    ├── urls.py
    └── wsgi.py
```
Nous allons écrire nos tests unitaires dans le module ./lists/test.py. Les tests unitaires (TU) sont ceux élaborés du point de vue technique. Vous commencez à voir la méthode globale ?
> TF (point de vue utilisateur) => TU (point de vue réalisation technique) 

Voici un exemplevolontairement faux, de test unitaire, dans notre module /lists/test.py :
> lists/test.py
```python
from django.test import TestCase

class ExampleTest(TestCase):

    def test_addition_in_parallel_universe(self):
        self.assertEqual(1+1, 3)
```
On execute les tests unitaires de l'applications lists ainsi:
```bash
$ ./manage.py test lists/
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
F
======================================================================
FAIL: test_addition_in_parallel_universe (lists.tests.ExampleTest)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/tuto/todo-tdd/lists/tests.py", line 6, in test_addition_in_parallel_universe
    self.assertEqual(1+1, 3)
AssertionError: 2 != 3

----------------------------------------------------------------------
Ran 1 test in 0.001s

FAILED (failures=1)
Destroying test database for alias 'default'...
```

Comme prévu le test qui suppose que 1 + 1 font 3 ne passe pas ...  
On utilise la encore l'héritage de la classe TestCase, pour bénéficier de méthodes d'assertions.

Allez ! On a maintenant un TF, un TU, notre application lists, la machinerie est en place pour commencer ! Ca semble être le bon moment pour un petit commit.
```bash
$ git add .
$ git commit -m "Add new app lists, with a failing UT"
```

## Django un framework Models Views Templates :

Allez la encore un petit croquis:
<image src="./images/django-simplifie.png" alt="django-simplifé" style="border-radius:10px" border="2 solid" width="800" >

> 1 - Une requête HTTP est envoyée à destination d'une url  
> 2 - Django utilise le module urls pour diriger la requête vers la vue associée.  
> 3 - La vue traite la requête et retourne une réponse HTTP.

Nous pouvons donc commencer par tester si la résolution entre le chemin d'url racine '/' et la fonction vue associée à notre page d'accueil est faite.

Pour trouver à quelle vue est associée un chemin d'url on utilise la fonction [resolve](https://docs.djangoproject.com/fr/3.1/ref/urlresolvers/#resolve):
```python
from django.urls import resolve
from django.test import TestCase
from lists.views import home_page

class HomePageTest(TestCase):

    def test_root_url_resolve_to_home_page_view(self):
        my_view = resolve('/')
        self.assertEqual(my_view.func, home_page)
```

On affirme que la résolution du chemin racine '/' renvoie vers la vue 'home_page' (qui n'existe pas encore, le test doit donc échouer);
```bash
$ ./manage.py test lists/
[ ... ]
ImportError: cannot import name 'home_page'

----------------------------------------------------------------------
Ran 1 test in 0.000s

FAILED (errors=1)
```
On ajoute:
>lists/views.py
```python
from django.shortcuts import render

home_page = None
```
Je vous entends d'ici dire "Mais c'est une blague !!!" :)  
Nan c'est pour décomposer et comprendre la démarche !  
On lance les tests encore:
```bash
$ ./manage.py test lists/
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
E
======================================================================
ERROR: test_root_url_resolve_to_home_page_view (lists.tests.HomePageTest)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/tuto/todo-tdd/lists/tests.py", line 8, in test_root_url_resolve_to_home_page_view
    my_view = resolve('/')
  File "/home/tuto/.local/share/virtualenvs/todo-tdd-rzjrtEdZ/lib/python3.6/site-packages/django/urls/base.py", line 24, in resolve
    return get_resolver(urlconf).resolve(path)
  File "/home/tuto/.local/share/virtualenvs/todo-tdd-rzjrtEdZ/lib/python3.6/site-packages/django/urls/resolvers.py", line 558, in resolve
    raise Resolver404({'tried': tried, 'path': new_path})
django.urls.exceptions.Resolver404: {'tried': [[<URLResolver <URLPattern list> (admin:admin) 'admin/'>]], 'path': ''}

----------------------------------------------------------------------
Ran 1 test in 0.001s

FAILED (errors=1)
Destroying test database for alias 'default'...
```
On peut lire à la fin une exception [Resolver404](https://docs.djangoproject.com/fr/2.2/ref/exceptions/#django.urls.Resolver404) qui nous indique que le chemin ne correspond à aucune vue. En nous rappelant du dessin sur django ci-dessus, nous allons nous rendre dans le modules superlists/urls.py pour résoudre ce problème.
>superlists/urls.py
```python
"""superlists URL Configuration

The `urlpatterns` list routes URLs to views. For more information please see:
    https://docs.djangoproject.com/en/2.2/topics/http/urls/
Examples:
Function views
    1. Add an import:  from my_app import views
    2. Add a URL to urlpatterns:  path('', views.home, name='home')
Class-based views
    1. Add an import:  from other_app.views import Home
    2. Add a URL to urlpatterns:  path('', Home.as_view(), name='home')
Including another URLconf
    1. Import the include() function: from django.urls import include, path
    2. Add a URL to urlpatterns:  path('blog/', include('blog.urls'))
"""
from django.contrib import admin
from django.urls import path

urlpatterns = [
    path('admin/', admin.site.urls),
]
```
On peut lire des commentaires bien utiles pour faire correspondre un chemin à une vue:
> superlists/urls.py
```python
from django.contrib import admin
from django.urls import path
from lists import views

urlpatterns = [
    path('', views.home_page, name='home'),
]
```
Et on relance le TU:
```bash
$ ./manage.py test lists/
[ ... ]
TypeError: view must be a callable or a list/tuple in the case of include().
```
Notre vue doit être un callable, ok modifions çca:
> lists/views.py
```python
from django.shortcuts import render

# Create your views here.
def home_page():
    pass
```
On relance le TU:
```bash
$ ./manage.py test lists/
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
.
----------------------------------------------------------------------
Ran 1 test in 0.001s

OK
Destroying test database for alias 'default'...
```
Génial ! Notre illustre premier test unitaire vient de passer ! Ca mérite bien un commit.
```bash
$ git commit -am "First UT, url root path mapped to empty home_page view"
```
Pour se rappeler