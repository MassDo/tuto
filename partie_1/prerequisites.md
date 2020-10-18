# Prerequisites - Conditions préalables.
Pour commencer ce projet il vous faut avoir:
> - [ ] Firefox 
> - [ ] Git
> - [ ] Python 3
> - [ ] Pipenv
> - [ ] Django 2.2
> - [ ] Selenium 3
> - [ ] Geckodriver

Vous pouvez commencer par vérifier que vous avez python, pip et pipenv avec:
```
$ python --version && pip --version && pipenv --version
```
Sinon on fait les installations:
```bash
$ apt-get install python3 python3-pip
```
Pour pipenv:
```bash
$ pip install --user pipenv
```
Si la commande __pipenv__ n'est pas accessible par la suite pas d'inquietude !
Il faut ajouter le repertoire utilisateur des binaires à votre [variable d'environnement](https://doc.ubuntu-fr.org/variables_d_environnement) PATH.

L'ajout du répertoire au PATH se fait comme cela:
```bash
$ echo 'export PATH="$PATH:$(python -m site --user-base)/bin"' >> ~/.profile
```
Avec cette commande on récupère le chemin des binaires de l'utilisateur __$(python -m site --user-base)/bin__, on l'ajoute à la variable PATH dans tous les environnements grace à ___export PATH=...___  et enfin on ajoute cette commande au fichier __~/.profile__ pour que cela soit valable à chaque nouveau shell de l'utilisateur.

Geckodriver:  
Il peut etre téléchargé [ici](https://github.com/mozilla/geckodriver/releases/), ou [la](https://github.com/mozilla/geckodriver/releases/download/v0.27.0/geckodriver-v0.27.0-linux64.tar.gz) pour un lien direct. Vous devez ensuite le décompresser et l'ajouter à /usr/sys/bin
```bash
$ sudo tar -xvf geckodriver-v0.27.0-linux64.tar.gz -C /usr/local/bin
```
-x pour extract, -v pour verbose, -f pour file ( à extraire) -C pour ajouter au répertoire suivant.
Vous pouvez maintenant supprimer l'archive dans le dossier où celle-ci a été téléchargée.
## Mise en place de l'environnement virtuel:
On va bientot pouvoir commencer ! Mais il nous reste encore à configurer l'environnement virtuel !  

Vous pouvez le créer dans le dossier qui accueilleras votre projet (pour moi todo-tdd):
```bash
pipenv --python 3.6 && \
pipenv install "django==2.2" && \
pipenv install --dev "selenium<4"
```
L'option --dev permet de signaler que cette dépendance est pour le  développement. Lors de la mise en production l'installation des dépendances par __pipenv install__ ne prendra pas en compte celles signalées par --dev. Pratique !

Alors résumons nos dépendances:

> - [x] Firefox 
> - [x] Git
> - [x] Python 3
> - [x] Pipenv
> - [x] Django 2.2
> - [x] Selenium 3
> - [x] Geckodriver  
> 
Ouf voila ! Normalement c'est bon tout est en place pour enfin commencer 👌 ! Vous pouvez activer votre venv grace à __pipenv shell__ maintenant.

Dans le prochain chapitre on commence les tests... Promis !  

Chapitre [suivant](chap1.md).











