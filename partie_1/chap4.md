# Chapitre 4 - Réusinage du code et templates

Pourquoi tout ces tests ?
Vous pouvez penser que la méthode est trop lente , mais au final la complexité croissantes des interactions peut aboutir à l'impossibilité de réusiner son code (modification du code sans changement de fonctionnalités).  Plus un système devient complexe et moins vous pourrez avoir l'intégralité des intéractions en mémoire, les erreurs seront inévitable et sans tests trouver leur origine sera impossible.  

Le TDD limite la taille du code, guide dans la conception et réduit la "dette technique" c'est à dire le temps qu'il faudra utiliser pour effectuer les maintenances corrective et évolutive dans le futur.

Allez on retourne au boulot. Humm d'ailleurs je suis un peu perdu mais on en était où déjà ? Ah oui utilisons notre TF pour savoir ! (Encore eux lol)  


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
Ran 1 test in 4.383s

FAILED (failures=1)
```
(Si vous avez une erreur vérifiez que vous avez bien lancé le server dans un autre shell avec  ```$ ./manage.py runserver```)

Ok notre TF échoue volontairement du fait de la méthode fail avec le message 'Finish the test'.  
Ah oui ca me revient, notre test vérifiant 'To-Do' dans le titre de la page est passé, mais il reste la suite de la user storie à tester.
> A faire:
>  - [ ] Continuer la rédaction du TF pour le reste de la US.  
> 
Bon bah c'est parti, on complète le TF !
> functional_tests.py
```python
import time 
import unittest

from selenium import webdriver
from selenium.webdriver.common.keys import Keys

class NewVisitorTest(unittest.TestCase):
    
    def setUp(self):
        self.browser = webdriver.Firefox()

    def tearDown(self):
        self.browser.quit()

    def test_can_start_a_list_and_retrieve_it_later(self):
        # 1 - Scénario "Robert découvre le site":
        # Etant donné Robert,
        #  un visiteur qui a entendu parler de notre site
        # Quand il saisit l'url de notre site via son navigateur
        self.browser.get('http://localhost:8000')
        # Alors il peut lire "To-Do" dans l'onglet,        
        self.assertIn('To-Do', self.browser.title)
        # Et dans un header sur la page.
        header_text = self.browser.find_element_by_tag_name('h1').text
        self.assertIn('To-Do', header_text)
        # Et on lui propose de saisir une note de texte.
        inputbox = self.browser.find_element_by_id('id_new_item')
        self.assertEqual(
            inputbox.get_attribute('placeholder'),
            'Enter a to-do item'
        )

        # 2 - Scénario "Robert ajoute une note":
        # Etant donné Robert (toujours lui !)
        #  un visiteur sur notre page d'accueil.
        # Quand il ajoute du texte ("Acheter du pain"), 
        #  dans la zone de texte
        inputbox.send_keys('Acheter du pain')
        # Et qu'il appuie sur entrée
        inputbox.send_keys(Keys.ENTER)
        time.sleep(1)
        # Alors sa note est ajoutée dans un tableau,
        #  sur la même page avec le numéro de la note devant.
        table = self.browser.find_element_by_id('id_list_table')
        rows = table.find_elements_by_tag_name('tr')
        self.assertTrue(
            any(r.text == '1: Acheter du pain' for r in rows)
        )
        
        # Echec volontaire du test
        self.fail('Finish the test!')
        # [suite des scénarios pour plus tard ...]
```
### Explications:  

On va pouvoir manipuler les éléments du DOM grâce à des méthodes de notre instance '[browser](https://www.selenium.dev/documentation/fr/webdriver/)' qui "représente" le navigateur. Ces méthodes sont par exemple [find_element_by_id()
](https://www.selenium.dev/documentation/fr/getting_started_with_webdriver/locating_elements/) pour localiser des éléments du DOM.

On va aussi simuler [la manipulation du clavier](https://www.selenium.dev/documentation/fr/webdriver/keyboard/) en utilisant la méthode send_keys() et la classe Keys, (ne pas oublier de l'importer !), pour les touches spéciales.

Le time.sleep est là pour être sur, que le navigateur ait le temps de charger la page, avant de faire l'assertion de test qui suit.

Enfin, attention, la methode find_elements_by_tag_name() retourne une liste d'éléments.

### On lance le TF: 
```bash
$ python functional_tests.py 
[ ... ]
selenium.common.exceptions.NoSuchElementException: Message: Unable to locate element: h1
```

Alors le TF n'arrive pas a trouver un header. C'était prévu, on a terminé de compléter notre TF on peut cocher notre case et faire un commit non ?! :)
> A faire:
>  - [x] Continuer la rédaction du TF pour le reste de la US. 
