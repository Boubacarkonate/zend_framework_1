# 6. Le fichier `application/Bootstrap.php` (version étendue)

Le **Bootstrap** initialise différentes ressources nécessaires au fonctionnement de l’application,  
comme la connexion à la base de données, la configuration des sessions, des routes personnalisées, etc.
- Toutes les méthodes commençant par _init sont appelées automatiquement pendant le bootstrap.
- L’ordre d’appel correspond à l’ordre d’apparition dans la classe.
- On peut ajouter autant d’_initXXX() qu'on veut pour organiser proprement l’initialisation.

---

```php
<?php
class Bootstrap extends Zend_Application_Bootstrap_Bootstrap
{
    /**
     * Initialise l'adaptateur de base de données
     */
    protected function _initDbAdapter()
    {
        $db = $this->getPluginResource('db')->getDbAdapter();
        Zend_Db_Table::setDefaultAdapter($db);
    }

    /**
     * Initialise la gestion des sessions
     */
    protected function _initSession()
    {
        Zend_Session::start();
    }

    /**
     * Ajoute des routes personnalisées au routeur FrontController
     */
    protected function _initRoutes()
    {
        $frontController = Zend_Controller_Front::getInstance();
        $router = $frontController->getRouter();

        // Exemple d'une route personnalisée
        $route = new Zend_Controller_Router_Route(
            'blog/:id',
            array(
                'controller' => 'blog',
                'action'     => 'view',
                'id'         => null,
            )
        );

        $router->addRoute('blogRoute', $route);
    }
}

```

## Explications de ces quelques méthodes
**_initDbAdapter()**
- Récupère la ressource db définie dans la config (application.ini).
- Définit cet adaptateur comme celui par défaut pour toutes les tables Zend via Zend_Db_Table.

**_initSession()**
- Démarre la gestion des sessions PHP avec Zend_Session.

- Utile pour gérer la connexion utilisateur, stocker des variables temporaires, etc.

**_initRoutes()**
- Récupère le routeur du Front Controller.

- Ajoute une route personnalisée : ici, une URL blog/:id qui pointe vers le contrôleur blog et l’action view.

- Permet d’avoir des URL plus propres et personnalisées.

