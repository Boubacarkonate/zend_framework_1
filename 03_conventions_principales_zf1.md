# Conventions Zend Framework 1 (ZF1)
ZF1 suit un ensemble strict de conventions qui facilitent l’organisation du code, la maintenabilité et permettent au framework de « deviner » où trouver les fichiers sans configurations complexes.

## 1. Structure des dossiers
- /application
  - /controllers ***(actions, logique de traitement)***
  - /models  ***(gestion des données, logique métier)***
  - /views/scripts/nom-controller ***(fichiers .phtml)***
- /public ***Document root (index.php, .htaccess)***
- /library ***Bibliothèques et classes tierces ou personnalisées***
- application.ini ***Configuration globale***
- Bootstrap.php ***Classe de démarrage (initialisation)***

## 2. Nommage des classes
### Contrôleurs
- Classes nommées avec le suffixe ``Controller``.
    - Le nom doit suivre la forme : ``NomController``
    - Par exemple : ``UserController``, ``PostController``.
- Les classes se trouvent dans `/application/controllers/`.
- Chaque méthode publique de la classe correspond à une action, suffixée par Action().
Exemple : ``listAction``(), ``viewAction``().

### Modèles 
- Classes nommées avec un préfixe, souvent ``Application_Model_``.
- Par exemple :
    - ``Application_Model_User``
    - ``Application_Model_Post``
- Les modèles sont dans ``/application/models/``.

### Vues
- Les vues sont des fichiers ``.phtml`` placés dans ``/application/views/scripts/nom-controller/``.
- Le dossier correspond au nom du contrôleur en minuscules (sans suffixe ``Controller``).
- Chaque action a son fichier de vue nommé ``action.phtml``.
Exemple : pour ``listAction()``dans ``PostController``, la vue sera dans :
``/application/views/scripts/post/list.phtml``

## 3. Correspondance URL => Controller/Action
- La structure URL par défaut est :
    - /module/controller/action/param1/val1/param2/val2/...
- En mode standard (sans modules), cela donne :
    - /controller/action/param1/val1/...
- Exemple :
/post/list appelle ``PostController::listAction()``

## 4. Autoloading des classes
ZF1 utilise l’autoloading basé sur les noms de classes avec le séparateur ``_``.

Une classe nommée Application_Model_User doit se trouver dans :
``/application/models/User.php``

Une classe ``Zend_Db_Table_Abstract`` est dans la bibliothèque Zend.

## 5. Fichiers clés
- **index.php** (dans ``/public``) : point d’entrée, initialise l’application.

- **Bootstrap.php** : initialise les ressources (bases de données, routes, plugins, etc.).

- **application.ini** : configuration globale (base de données, routes, environnement).

## 6. Helpers
ZF1 propose des helpers pour simplifier le code dans les contrôleurs, vues et layouts :
- ``$this->_helper->redirector()`` : redirection.
- ``$this->_helper->flashMessenger`` : messages temporaires entre requêtes.
- ``$this->view`` : accès aux variables passées à la vue.
- Helpers de vues (``$this->url()``, ``$this->partial()``, etc.).

## 7. Layout et Vue
- Par défaut, ZF1 affiche la vue associée à l’action.
- Le Layout (via Zend_Layout) permet de définir un template général (header, footer, etc.).
- Layout par défaut se trouve dans ``/application/layouts/scripts/layout.phtml``.

## 8. Convention pour les paramètres
- Les paramètres passés en URL ou en POST sont accessibles via :
    - ``$this->getRequest()->getParam('nom')``
    - ``$this->getRequest()->getPost('champ')``
- ZF1 gère les filtres et validateurs via ``Zend_Filter`` et ``Zend_Validate``.


## 9. Convention pour la base de données
- Utilisation de ``Zend_Db_Table`` pour représenter les tables.
- Nom des classes en camelCase avec suffixe Table :
``Application_Model_DbTable_Users`` pour la table ``users``.
- Chaque méthode dans le modèle utilise l’API Zend_Db pour les requêtes.

