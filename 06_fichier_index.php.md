# 5. Le Front Controller (public/index.php)

Ce fichier est le point d’entrée d’une application Zend Framework.
Il :
- Définit les chemins et l’environnement,
- Configure le chargement des bibliothèques,
- Charge et initialise l’application Zend,
- Démarre l’exécution de l’application.

Cela permet une architecture claire, modulaire, et adaptée aux environnements multiples.

# Explication du script PHP

```php
<?php
defined('APPLICATION_PATH') 
    || define('APPLICATION_PATH', realpath(dirname(__FILE__) . '/../application'));

defined('APPLICATION_ENV') 
    || define('APPLICATION_ENV', getenv('APPLICATION_ENV') ?: 'development');

set_include_path(implode(PATH_SEPARATOR, array(
    realpath(APPLICATION_PATH . '/../library'),
    get_include_path(),
)));

require_once 'Zend/Application.php';

$application = new Zend_Application(
    APPLICATION_ENV,
    APPLICATION_PATH . '/configs/application.ini'
);

$application->bootstrap()->run();
```

## 1. Définition de APPLICATION_PATH

```php
defined('APPLICATION_PATH') 
    || define('APPLICATION_PATH', realpath(dirname(__FILE__) . '/../application'));
```
- Vérifie si la constante APPLICATION_PATH est déjà définie.
- Sinon, la définit en utilisant le chemin absolu vers le dossier application, situé un niveau au-dessus du fichier courant.

- realpath() résout le chemin complet, et dirname(__FILE__) donne le dossier du fichier courant.

## 2. Définition de APPLICATION_ENV
```php
defined('APPLICATION_ENV') 
    || define('APPLICATION_ENV', getenv('APPLICATION_ENV') ?: 'development');
```
- Vérifie si la constante APPLICATION_ENV est définie.
- Sinon, la définit à partir de la variable d’environnement APPLICATION_ENV.
- Si cette variable n’existe pas, la valeur par défaut est 'development'.
- Cela permet de gérer différents environnements (dev, test, prod).

## 3. Configuration du include_path
```php
set_include_path(implode(PATH_SEPARATOR, array(
    realpath(APPLICATION_PATH . '/../library'),
    get_include_path(),
)));
```
- Modifie la variable include_path de PHP, qui détermine où PHP cherche les fichiers inclus (require / include).
- Ajoute le dossier library (un niveau au-dessus de application) en début de chemin.
- Cela permet de charger automatiquement les bibliothèques personnalisées ou tierces comme Zend Framework.

## 4. Chargement de la classe Zend_Application
```php
require_once 'Zend/Application.php';
```
- Inclut la classe principale Zend_Application du framework Zend.
- Cette classe gère l’application, son bootstrap, et son cycle de vie.

## 5. Instanciation de l’application Zend
```php
$application = new Zend_Application(
    APPLICATION_ENV,
    APPLICATION_PATH . '/configs/application.ini'
);
```
- Crée une instance de l’application Zend.
- Le premier argument indique l’environnement courant (development, production, etc.).
- Le second argument est le chemin vers le fichier de configuration (application.ini), qui contient les paramètres de l’application (base de données, options, etc.).

## 6. Initialisation et lancement de l’application
```php
$application->bootstrap()->run();
```
- Lance la méthode bootstrap() qui initialise tous les composants nécessaires (base de données, sessions, etc.).
- Ensuite, run() démarre l’application, gérant la requête HTTP entrante et générant la réponse.