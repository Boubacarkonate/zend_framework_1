# 5. Le fichier application/configs/application.ini

Centralise la configuration pour différents environnements (production, development),
comme la base de données, le contrôleur, la gestion des erreurs, les chemins, etc.

---

## Contenu du fichier

```ini
[production]
phpSettings.display_startup_errors = 0
phpSettings.display_errors = 0

includePaths.library = APPLICATION_PATH "/../library"
bootstrap.path = APPLICATION_PATH "/Bootstrap.php"
bootstrap.class = "Bootstrap"

resources.frontController.controllerDirectory = APPLICATION_PATH "/controllers"
resources.frontController.params.displayExceptions = 0

resources.db.adapter = "PDO_MYSQL"
resources.db.params.host = "localhost"
resources.db.params.username = "root"
resources.db.params.password = ""
resources.db.params.dbname = "ma_base"

[development : production]
phpSettings.display_startup_errors = 1
phpSettings.display_errors = 1
resources.frontController.params.displayExceptions = 1
```

## Explications du contenu

## Section [production] ##
### phpSettings.display_startup_errors = 0
- Désactive l’affichage des erreurs de démarrage PHP, ce qui évite de montrer des messages d’erreur sensibles en production.

### phpSettings.display_errors = 0 
- Désactive l’affichage général des erreurs PHP pour éviter que les visiteurs voient des messages d’erreur.

### includePaths.library = APPLICATION_PATH "/../library"  
- Ajoute le dossier library (situé un niveau au-dessus du dossier application) au chemin d’inclusion PHP.
- Cela permet de charger les bibliothèques comme Zend Framework.

### bootstrap.path = APPLICATION_PATH "/Bootstrap.php"  
- Chemin vers le fichier Bootstrap qui initialise les ressources de l’application.

### bootstrap.class = "Bootstrap"  
- Nom de la classe Bootstrap à instancier.

### resources.frontController.controllerDirectory = APPLICATION_PATH "/controllers"  
- Dossier où se trouvent les contrôleurs de l’application.

### resources.frontController.params.displayExceptions = 0  
- Ne pas afficher les exceptions dans le navigateur, pour la sécurité en production.

## Configuration base de données : 

### resources.db.adapter = "PDO_MYSQL"  : 
utilise l’adaptateur PDO MySQL.

### resources.db.params.host = "localhost"  : 
serveur de base de données local.

### resources.db.params.username = "root"  :  
utilisateur MySQL.

### resources.db.params.password = ""   : 
mot de passe vide.

### resources.db.params.dbname = "ma_base"  : 
nom de la base de données.


## Section [development : production] 
- Cette section hérite de la configuration [production] grâce à : production.
- Elle surcharge uniquement quelques valeurs pour l’environnement de développement :

 ### phpSettings.display_startup_errors = 1  
- Affiche les erreurs de démarrage PHP, utile pour le debug.

 ### phpSettings.display_errors = 1  
- Affiche toutes les erreurs PHP à l’écran.

 ### resources.frontController.params.displayExceptions = 1  
- Affiche les exceptions détaillées dans le navigateur pour faciliter le débogage.
