# Chapitre 2 - Amélioration du TF et utilisation du module unittest.

Bon, par ou commencer pour répondre à notre user storie du chapitre précédent, et faire notre app todo-list ?  

Nous allons nous faire guider par un test fonctionnel ! il nous aidera à "cerner" notre "Minimum Viable App" soit la chose la plus simple que nous pouvons construire, pour répondre à la fonctionnalitée découlant de la user storie.

>Pour rappel notre user storie:  
>
> __En tant__ <font color="DC143C">qu'utilisateur d'un navigateur web,</font> __Je veux__ <font color="483D8B">pouvoir ajouter des notes, </font> __Afin que__  <font color="008000">je puisse les retrouver plus tard.</font>

C'est parti modifions notre ./functional_tests.py en y ajoutant en commentaires les scénarios de tests du chapitre précédent:

```python
from selenium import webdriver

browser = webdriver.Firefox()

# Scénario "Robert découvre le site":
# Etant donné Robert un visiteur qui a entendu parler de notre site
# Quand il saisit l'url de notre site via son navigateur
browser.get('http://localhost:8000')
# Alors il peut lire "To-Do" dans l'onglet
assert 'To-Do' in browser.title
# Et on lui propose de saisir une note de texte.


# [suite des scénarios pour plus tard ...]

browser.quit()
```

On vient de modifier l'assertion; on affirme que 'To-Do' est dans le titre de la page.

Le test est écrit, démarrons le serveur de dev et lançons notre TF !
>./todo-tdd
```bash
$ ./manage.py runserver
```
et dans un autre shell (venv activé)
```bash
$ python functional_tests.py
```
Une fenêtre Firefox s'ouvre sur localhost mais rien ne se passe et un message d'erreur arrive:
```bash
$ python functional_tests.py 
Traceback (most recent call last):
  File "functional_tests.py", line 10, in <module>
    assert 'To-Do' in browser.title
AssertionError
```
C'est ce quon appel une 'expected fail' car nous attendions cette erreur. En effet 'To-Do' ne peut pas etre dans le titre de la page car nous n'avons rien implémenté encore.

## Module Unittest:

Pour formater la visibilité des messages d'erreurs des tests, le module python unittest est disponible, nous allons l'utiliser!

Changons à nouveau notre TF:
> functional_test.py
```python
from selenium import webdriver
import unittest

class NewVisitorTest(unittest.TestCase):
    
    def setUp(self):
        self.browser = webdriver.Firefox()

    def tearDown(self):
        self.browser.quit()

    def test_can_start_a_list_and_retrieve_it_later(self):
        # Scénario "Robert découvre le site":
        # Etant donné Robert un visiteur qui a entendu parler de notre site
        # Quand il saisit l'url de notre site via son navigateur
        self.browser.get('http://localhost:8000')
        # Alors il peut lire "To-Do" dans l'onglet
        self.assertIn('To-Do', self.browser.title)
        # ON FAIT ECHOUER LE TEST VOLONTAIREMENT CAR INCOMPLET
        self.fail('FInish the test!')
        # Et on lui propose de saisir une note de texte.


        # [suite des scénarios pour plus tard ...]

if __name__ == '__main__':
    unittest.main()
```
### Alors ...
on place notre test dans une classe qui hérite de unittest.TestCase et on défini nos test avec un titre explicite qui commence par test_ pour être pris en compte.  

De plus la classe TestCase du module unittest nous permet d'utiliser des methodes biens pratiques!

Pour établir des conditions avant et après chaque tests on peut utiliser les méthodes setUp() et tearDown(). Pour vérifier notre assertion on utilise la méthode assertIn(). Pour faire échouer volontairement notre test on peut utiliser la méthode fail().

### <font style="color: red;"> Pour résumer:</font>  
on va placer nos test dans une classe qui hérite de unnittest.[TestCase](https://docs.python.org/fr/3.6/library/unittest.html#unittest.TestCase), celle ci va nous fournir des méthodes de configuration et d'[assertion](https://docs.python.org/fr/3.6/library/unittest.html#assert-methods) pratiques. Nos test devront avoir des noms explicites et commençant par test_ pour être exécutés.

Voila vous avez compris, maintenant essayons le notre nouveau FT!
>functional_tests.py
```bash
$ python functional_tests.py 
F
======================================================================
FAIL: test_can_start_a_list_and_retrieve_it_later (__main__.NewVisitorTest)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "functional_tests.py", line 18, in test_can_start_a_list_and_retrieve_it_later
    self.assertIn('To-Do', self.browser.title)
AssertionError: 'To-Do' not found in 'Django: the Web framework for perfectionists with deadlines.'

----------------------------------------------------------------------
Ran 1 test in 4.254s

FAILED (failures=1)
```

Ahhhh, le test est un echec comme prévu, mais au moins c'est plus lisible, voir beau !

Faisons un commit avant de prendre un petite pause bien méritée:  
 ☕

```bash
$ git commit -am "FT with unittest"
```
l'option -a c'est pour ajouter les fichier déjà suivi!

La suite au chapitre [suivant](chap3.md)



 
