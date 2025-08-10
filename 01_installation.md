# Installation manuelle de Zend Framework 1 (ZF1-Future) sur WAMP avec PHP 8.2

---

## 🎯 Objectif final

Avoir un projet Zend Framework 1 fonctionnel sur PHP 8.2 et +, avec la version modernisée **zf1-future** clonée manuellement ***sans Composer***.

---

## 🧱 Étape 1 – Créer la structure du projet

Ouvre PowerShell ou l’explorateur et lance :

```powershell
cd C:\wamp64\www
mkdir monDossier
cd monDossier
mkdir application public library
cd library
git clone https://github.com/shardj/zf1-future.git Zend
```

OU

Si pas de git clone :
- Sur : https://github.com/shardj/zf1-future
- Clique sur Code > Download ZIP
- Décompresse l’archive dans C:\wamp64\www\zf1-manuel\library\Zend

## 🛠️ Étape 3 – Créer le fichier public/index.php

Crée ``C:\wamp64\www\zf1-manuel\public\index.php`` avec :

```php
    <?php
// Chemin vers l'application
define('APPLICATION_PATH', realpath(dirname(__FILE__) . '/../application'));

// Inclure la bibliothèque Zend
set_include_path(implode(PATH_SEPARATOR, [
    realpath(APPLICATION_PATH . '/../library/Zend/library'),
    get_include_path(),
]));

require_once 'Zend/Application.php';

// Créer l'application Zend
$application = new Zend_Application(
    'development',
    APPLICATION_PATH . '/configs/application.ini'
);

$application->bootstrap()->run();

```

## 🛠️ Étape 4 – Créer le fichier de configuration
Crée ``C:\wamp64\www\zf1-manuel\application\configs\application.ini`` avec :

```ini
[production]
phpSettings.display_startup_errors = 0
phpSettings.display_errors = 0

includePaths.library = APPLICATION_PATH "/../library/Zend/library"
bootstrap.path = APPLICATION_PATH "/Bootstrap.php"
bootstrap.class = "Bootstrap"

autoloadernamespaces[] = "Zend_"
resources.frontController.controllerDirectory = APPLICATION_PATH "/controllers"

[development : production]
phpSettings.display_startup_errors = 1
phpSettings.display_errors = 1

```

## 👨‍💻 Étape 5 – Créer un contrôleur
Crée ``C:\wamp64\www\zf1-manuel\application\controllers\IndexController.php`` avec :

```php
<?php
class IndexController extends Zend_Controller_Action
{
    public function indexAction()
    {
        $this->view->message = "Hello ZF1-Future on PHP 8.2!";
    }
}

```

## 📄 Étape 6 – Créer la vue
Crée ``application/views/scripts/index/index.phtml`` avec :

```phtml
<h1><?php echo $this->message; ?></h1>
```


## 🛠️ Étape 7 – Créer le bootstrap
Crée ``application/Bootstrap.php`` avec :

```php
<?php
class Bootstrap extends Zend_Application_Bootstrap_Bootstrap
{
}
```


## ▶️ Étape 8 – Lancer le serveur PHP

Dans PowerShell :

```powerShell
cd C:\wamp64\www\zf1-manuel\public
php -S localhost:8080
```
Puis ouvrir navigateur :
http://localhost:8080

## ✅ Résultat attendu
S’afficherera dans le navigateur :

**Hello ZF1-Future on PHP 8.2!**