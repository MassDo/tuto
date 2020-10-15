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
> - <font color="008000">__Alors__</font> il arrive sur une page d'accueil, qui lui propose de saisir une note de texte.
>   - <font color="008000">__Et__</font> il peut lire "To-Do" dans l'onglet de son navigateur.

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

Mais nous allons expliquer cela dans le prochain chapitre, chaque chose en son temps.

Maintenant que nous savons créer notre environnement virtuel,  que nous avons nos dépendances et des bases pour approcher les notions de tests, revenons à la création de notre projet Django !

## Un test en premier !

Vous étiez tenté de commencer directement la création du projet django, mais ca serait déroger au mantra "Un test en premier !"

Nous allons donc créer dans notre dossier racine ./todo-tdd un fichier functional_tests.py


lien chapitre [suivant]()





