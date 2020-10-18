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
Nous allons écrire nos tests unitaires dans le module ./lists/test.py. Les tests unitaires (TU) sont ceux élaborés du point de vue technique. Vous commencez à voir la mécanique globale ? 
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

## Django un framework M.V.T

Allez, la encore un petit croquis, pour expliquer l'organisation en Modèles Vues Templates de Django:  

<image src="./images/django-simplifie.png" title="Django simplifié" alt="django-simplifé" style="border-radius:10px" border="2 solid" width="800" >

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
Je vous entends d'ici dire "Mais c'est une blague !!!", On va pas autant décomposer ! 🤯  
Ahah ! Pas d'inquiétude, on ira plus vite après, mais pour l'instant, on cherche à bien s'imprénier de la méthode ! 🤓  
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
On peut lire à la fin une exception [Resolver404](https://docs.djangoproject.com/fr/2.2/ref/exceptions/#django.urls.Resolver404) qui nous indique que le chemin ne correspond à aucune vue. En nous rappelant du dessin sur django [ci-dessus](#django-un-framework-mvt), nous allons nous rendre dans le modules superlists/urls.py pour résoudre ce problème.
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
On peut lire des commentaires bien utiles pour faire correspondre un chemin à une vue, utilisons la méthode path() indiqué pour faire correspondre le chemin racine vers notre vue 'home_page':
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
Notre vue doit être un callable, ok modifions ça:
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
Génial ! Notre illustre premier test unitaire vient de passer ! Notre chemin d'url racine est bien relié à notre vue home_page ! Ca mérite bien un commit ... 
```bash
$ git commit -am "First UT, url root path mapped to empty home_page view"
```
Ok, il nous reste quoi à faire du coup ? ... Ah oui ! notre Test Fonctionnel vérifie que 'To-Do' est bien dans le titre de la page d'accueil. Du point de vue technique écrivons un test unitaire qui permet de vérifier cela ("Test first !").
> lists/tests.py
```python
from django.http import HttpRequest
from django.urls import resolve
from django.test import TestCase
from lists.views import home_page

class HomePageTest(TestCase):

    def test_root_url_resolve_to_home_page_view(self):
        my_view = resolve('/')
        self.assertEqual(my_view.func, home_page)

    def test_to_do_in_title_home_page(self):
        request = HttpRequest()
        response = home_page(request)
        html = response.content.decode('utf8')
        self.assertTrue(html.startswith('<html>'))
        self.assertIn('<title>To-Do</title>', html)
        self.assertTrue(html.endswith('</html>'))
```
Alors on vient de rajouter un test au titre explicite, laissez moi vous expliquer les étapes:
> - On instancie une requête depuis la classe [HttpRequest](https://docs.djangoproject.com/fr/3.1/ref/request-response/#django.http.HttpRequest)
> - Cette requête est transmise à la vue home_page qui retourne une réponse.
> - Cette réponse au contenu binaire est décodé en 'utf8'
> - On affirme que le contenu commence par une balise html
> - On affirme que 'To-Do' est dans le contenu de la réponse
> - On affirme que le contenu termine par une balise html

Allez c'est parti on applique le TDD: red => green with minimal code => refactor.  

__On lance les TU:__
>lists/tests.py
```bash
$ ./manage.py test lists/
[ ... ]
TypeError: home_page() takes 0 positional arguments but 1 was given
```
Ok modifions notre vue home_page pour quelle puisse prendre une requete en paramètre.  

__On ajuste :__
> lists/views.py
```python
from django.shortcuts import render
    
# Create your views here.
def home_page(request):
    pass
```
__On lance les TU :__
>lists/tests.py
```bash
$ ./manage.py test lists/
[ ... ]
html = response.content.decode('utf8')
AttributeError: 'NoneType' object has no attribute 'content'
```
Ok la réponse 'response' de notre vue est 'vide' et ne contient donc pas d'attribut 'content', elle doit en effet retourner un objet de classe [HttpResponse](https://docs.djangoproject.com/fr/3.1/ref/request-response/#django.http.HttpResponse).  

__On ajuste :__
> lists/views.py
```python
from django.shortcuts import render
from django.http import HttpResponse

# Create your views here.
def home_page(request):
    return HttpResponse()
```
__On lance les TU :__
>lists/tests.py
```bash
$ ./manage.py test lists/
[ ... ]
self.assertTrue(html.startswith('<html>'))
AssertionError: False is not true
```
Ok, maintenant la réponse est bien une HttpResponse mais le contenu ne commence pas par html.  

____On ajuste:____
> lists/views.py
```python
def home_page(request):
    return HttpResponse('<html>')
```
__On lance les TU :__
>lists/tests.py
```bash
self.assertIn('<title>To-Do</title>', html)
AssertionError: '<title>To-Do</title>' not found in '<html>'
```
__On ajuste :__
> lists/views.py
```python
def home_page(request):
    return HttpResponse('<html><title>To-Do</title>')
```
__On lance les TU :__
>lists/tests.py
```bash
self.assertTrue(html.endswith('</html>'))
AssertionError: False is not true
```
__On ajuste :__
> lists/views.py
```python
def home_page(request):
    return HttpResponse('<html><title>To-Do</title></html>')
```
__On lance les TU :__
>lists/tests.py
```bash
$ ./manage.py test lists/
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
..
----------------------------------------------------------------------
Ran 2 tests in 0.002s

OK
Destroying test database for alias 'default'...
```
Ok c'est bon les TU passent !! :)  🚀  
Bon on aurait pu écrire le code plus vite mais c'était pour vous montrer le processus d'itération et la recherche de la plus petite implémentation de code à chaque étape. TDD !  

Bon maintenant que nos TU passent vérifions notre TF.  
(N'oubliez pas de lancer votre server avec ```./manage runserver```)

__TF :__
> functional_tests.py
```bash
$ python functional_tests.py 
F
======================================================================
FAIL: test_can_start_a_list_and_retrieve_it_later (__main__.NewVisitorTest)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "functional_tests.py", line 20, in test_can_start_a_list_and_retrieve_it_later
    self.fail('Finish the test!')
AssertionError: Finish the test!

----------------------------------------------------------------------
Ran 1 test in 4.425s

FAILED (failures=1)
```
Quoi ! Le test fonctionnel ne passe pas ?! Ah si c'était la méthode fail() que nous avions mise pour faire échouer le test si celui ci n'était pas terminé ! Ouf notre travail à payé ! #HappyFace
Nous avons implémenté une page web avec "To-Do" dans le titre, le tout en BDD et TDD avec tests fonctionnel automatique ! Félicitation !

<image src="./images/chap3-todo.png" alt="todo title" title="Title is 'To-Do'" style="border-radius:10px" border="2 solid" width="800">

Ca mérite bien un ptit commit, allez:  
  
```bash
$ git commit -am "home_page view returns minimal HTML"
```
Un résumé de notre avancée:  
```bash
$ git log --oneline 
fb66add (HEAD -> master, origin/master) home_page view returns minimal HTML
0dd8db6 First UT, about url root path mapped to empty home_page view
dacc94a Add new app lists, with a failing UT
e38e5b9 FT with unittest
de3bc47 First commit: First FT and basic Django config
```
Maintenant on sait:
> - [x] Démarrer une application Django
> - [x] Utiliser la commande ./manage.py test 
> - [x] La différence entre TF et TU.
> - [x] La résolution de chemin d'url vers les vues associé grace à Urls.py
> - [x] Une vue et les objets request et response.
> - [x] Retourner un HTMLbasique.

Allez p'tite pause ☕ , et on se retrouve au chapitre [suivant](chap4.md) .

