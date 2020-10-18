# chapitre 1 - Django et test fonctionnel.

Le but de ce tuto est de découvrir le TDD (et un peu de BDD aussi). Pour cela nous allons utiliser une application web toute simple; une todo liste! Et oui !
## BDD, stories, scénarios et tests fonctionnels !
Pour simplifier, le BDD (Behavior Driven development) est basé sur des stories. Ce sont de petites phrases descriptives, où l'on se demande du point de vu de l'utilisateur  __qui__ veut __quoi__ et __pouquoi__. On peut les formaliser ainsi:  

> En tant que <font color="DC143C">[type d'utilisateur]</font>, je veux <font color="483D8B">[une action]</font> afin que <font color="008000">[un bénéfice, une valeur]</font>

Et elles doivent répondre au sigle "INVEST", dans la belle langue de shakespeare:
> - Independent
> - Negotiable
> - Valuable
> - Estimable
> - Small
> - Testable  

(Un exemple arrive un peu de patience)

Ces stories pourront etre détaillées en scénarios, ou l'on précisera un __acteur__ des __actions__ et des __conséquences__ avec les fameux mots clés __GIVEN__, __WHEN__, __THEN__ ... (vous en avez forcément entendu parler)

Nous pouvons voir les stories comme des désirs d'utilisation et les scénarios comme les descriptions de leur réalisation.

Et les tests fonctionnels dans tout ca ?

j'allais le dire ! Les tests fonctionnels sont la pour valider les scenarios. Voila c'est tout.

Oula, c'est assez abstrait tout ça, peut etre qu'un exemple serait utile...  

Ca tombe bien, j'ai une mémoire de poisson rouge et je veux faire une todo-list, accessible par navigateur web, pour me souvenir de ce que je dois faire !


Exemple de user storie pour notre todo liste: 
>__En tant__ <font color="DC143C">qu'utilisateur d'un navigateur web,</font> __Je veux__ <font color="483D8B">pouvoir ajouter des notes, </font> __Afin que__  <font color="008000">je puisse les retrouver plus tard.</font>

Essayons maintenant de détailler la storie avec un scénario:

Scénario "Robert découvre le site":
> - <font color="DC143C">__Etant donné__</font> Robert un visiteur qui a entendu parler de notre site todo-list
> - <font color="483D8B">__Quand__</font> il saisit l'url de notre site via son navigateur
> - <font color="008000">__Alors__</font> il peut lire "To-Do" dans l'onglet de la page.
>   - <font color="008000">__Et__</font> on lui propose de saisir une note de texte.

Scénario "Robert ajoute une note":
> - <font color="DC143C">__Etant donné__</font> Robert (toujours lui !) un visiteur qui est sur notre page d'accueil.
> - <font color="483D8B">__Quand__</font> il ajoute du texte dans la zone de texte
>   - <font color="483D8B">__Et__</font> qu'il appuie sur entrée
> - <font color="008000">__Alors__</font> sa note est ajoutée dans la page
>   - <font color="008000">__Et__</font> il peut à nouveau ajouter une note dans une zone de texte.

Scénario "Robert veut retrouver ses notes":
> - <font color="DC143C">__Etant donné__</font> Robert, notre visiteur qui est de retour sur notre page d'accueil, après une semaine de vacance dans le juras sous la pluie.
> - <font color="483D8B">__Quand__</font> il ajoute 3 notes via notre page d'accueil
> - <font color="008000">__Alors__</font> ses notes sont ajoutées 
>   - <font color="008000">__Et__</font> il obtient une url unique grace au numéro d'identification de sa liste

Chaque scénario fera l'objet d'un test de validation automatique. Nous utiliserons pour cela Selenium.

Mais, nous nous allons trop vite là ! Patience... 

Et le __TDD__ (Test Driven Development) dans tout ça ?!

Et bien le TDD est similaire au BDD mais du point de vue du développeur.

Allez comme disait Napoléon "Un bon croquis vaut mieux qu'un long discours." Je me suis risqué à essayer de résumer la situation !:  

<image src="./images/croquis-bdd-tdd.png" alt="croquis-bdd-tdd" title="Croquis; BDD et TDD" style="border-radius:10px" border="2 solid" width="800">

Maintenant que nous savons créer notre environnement virtuel,  que nous avons nos dépendances et des bases pour approcher les notions de tests, revenons à la création de notre projet Django !

## Un test en premier !

Vous étiez tenté de commencer directement la création du projet django, mais ca serait déroger au mantra "Un test en premier !"

Nous allons donc créer dans notre dossier racine ./todo-tdd un fichier functional_tests.py dans lequel nous allons ajouter:

```python
from selenium import webdriver

browser = webdriver.Firefox()
browser.get('http://localhost:8000')

assert 'Django' in browser.title
```

On lance le test qui devrait échouer

```bash
$ python functional_tests.py 
Traceback (most recent call last):
  File "functional_tests.py", line 4, in <module>
    browser.get('http://localhost:8000')
  File "/home/tuto/.local/share/virtualenvs/todo-tdd-rzjrtEdZ/lib/python3.6/site-packages/selenium/webdriver/remote/webdriver.py", line 333, in get
    self.execute(Command.GET, {'url': url})
  File "/home/tuto/.local/share/virtualenvs/todo-tdd-rzjrtEdZ/lib/python3.6/site-packages/selenium/webdriver/remote/webdriver.py", line 321, in execute
    self.error_handler.check_response(response)
  File "/home/tuto/.local/share/virtualenvs/todo-tdd-rzjrtEdZ/lib/python3.6/site-packages/selenium/webdriver/remote/errorhandler.py", line 242, in check_response
    raise exception_class(message, screen, stacktrace)
selenium.common.exceptions.WebDriverException: Message: Reached error page: about:neterror?e=connectionFailure&u=http%3A//localhost%3A8000/& [...]
```
Ok, on vient de voir une fenêtre firefox s'ouvrir toute seule, et qui essaye de se connecter sans succes au localhost.
Dans la console on voit une erreur. Bon c'est pas très lisible mais on comprend que ca ne fonctionne pas !  
 On arrive tout de même à lire connectionFailure à la fin ...

Maintenant qu'on a un test en échec, on peut écrire le code minimal pour que celui-ci passe.  
Dans notre cas essayons:
```bash
django-admin.py startproject superlists .
```
Cet outil de django permet de creer un projet "superlists" dans notre répertoire courant grace au point à la fin. Ne l'oubliez pas sinon django créera un répertoire ./todo-tdd/superlists dans lequel il mettra le projet superlists, un répertoire en trop pour nous...

L'arborescence de votre projet doit donc ressembler à cela:  
> ./todo-tdd
```
.
├── functional_tests.py
├── geckodriver.log
├── manage.py
├── Pipfile
├── Pipfile.lock
└── superlists
    ├── __init__.py
    ├── settings.py
    ├── urls.py
    └── wsgi.py
```
On remarque ```manage.py``` c'est la boîte à outils de django ! Elle nous permet de lancer un server de dev avec la commande 

```bash
$ ./manage.py runserver
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).

You have 17 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.

October 15, 2020 - 10:33:38
Django version 2.2, using settings 'superlists.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

On peut pour l'instant ignorer le message sur les migrations nous en parlerons plus tard quand nous aborderons la base de données.  
Notre serveur est normalement en service en local ! Lançonsnotre test fonctionnel depuis un nouveau shell, pour voir s'il passe ! (si vous fermez la console où le serveur est lancé alors vous allez l'arreter)
>./todo-tdd
```bash
$ python functional_tests.py
``` 
Une fenetre Firefox s'ouvre et se connecte a notre site, vous pouvez voir Django dans le tritre de l'onglet et une petite fusée ! La console n'affiche plus d'erreur, ouf, toute mes félicitations c'est notre premier test qui passe !!  

<image src="images/django-launch-chap1.png" alt="Django-launched-local-server" title="Django launched on local dev server" style="border-radius:10px" border="2 solid" width="800">


> Si vous avez un message d'erreur, c'est que vous avez sans doute, oublié d'activer votre environnement virtuel avec ```$ pipenv shell```.

Il reste une chose à faire avant de terminer ce chapitre, et que notre ordinateur tombe en panne ! 
Envoyer notre travail sur Github !  

On initialise notre git repository:
> ./todo-tdd  


```bash
$ git init .
```
```bash
$ ls
db.sqlite3           geckodriver.log  Pipfile       superlists
functional_tests.py  manage.py        Pipfile.lock
```
On voit plusieurs fichers que l'ont ne veut pas commiter; db.sqlite3 est le ficher de la base de donnée, et geckodriver.log celui des logs de notre browser de test.   

Pour les ignorer lors de nos commits, ajoutons les à un ficher .gitignore:

```bash
$ echo geckodriver.log >> .gitignore
$ echo db.sqlite3 >> .gitignore
```
Le double chevron '>>' ajoute à __la suite__ d'un ficher ( et le crée si il n'éxiste pas )

```bash
$ git status
Sur la branche master

Aucun commit

Fichiers non suivis:
  (utilisez "git add <fichier>..." pour inclure dans ce qui sera validé)

	.gitignore
	Pipfile
	Pipfile.lock
	functional_tests.py
	manage.py
	superlists/

aucune modification ajoutée à la validation mais des fichiers non suivis sont présents (utilisez "git add" pour les suivre)
```
Voila les fichers geckodriver.log et db.sqlite3 ne sont plus présents, nous pouvont ajouter les fichers et les commiter:
```bash
$ git add .
$ git commit -m "First commit: First FT and basic Django config"
```
Ajouter votre répertoire distant:
```bash
$ git remote add origin <url de votre repo github>
```
Et l'envoyer:
```bash
$ git push origin master
```
C'est fini ! Vous pouvez mettre votre siege en position horizontale et vous congratuler le premier chapitre est terminé. Vous avez établie une mise en place basique d'un projet django grace a un test fonctionel et commité tout ca. Un petite pause et on continue avec le [chapitre 2](chap2.md)





