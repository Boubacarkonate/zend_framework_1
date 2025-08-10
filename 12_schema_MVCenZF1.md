# Schéma MVC Zend Framework 1

<pre>
<span style="color:lightblue;">[Navigateur]</span>
     |
     | <span style="color:orange;">Requête HTTP (ex: /article/lire/id/15)</span>
     v
<span style="color:lightgreen;">[public/.htaccess]</span>  (réécriture URL)
     |
     v
<span style="color:lightgreen;">[public/index.php]</span>  (Front Controller unique)
     |
     v
<span style="color:cyan;">[Zend_Application]</span> initialise la config (application.ini) + Bootstrap.php
     |
     v
<span style="color:cyan;">[Zend_Controller_Router]</span> analyse l’URL -> trouve Module / Controller / Action
     |
     v
<span style="color:cyan;">[Dispatcher]</span> appelle Controller & Action
     |
     v
<span style="color:yellow;">[Controller (ex: ArticleController::lireAction())]</span>
     |
     |----> Utilise <span style="color:yellow;">Model</span> (ex: ArticleModel) pour récupérer données
     |                 |
     |                 v
     |         <span style="color:pink;">[Base de données]</span>
     |
     v
Prépare les données -> passe à la Vue
     |
     v
<span style="color:violet;">[Vue (fichier .phtml)]</span>
     |
     v
<span style="color:orange;">Sortie HTML (ou JSON si demandé)</span>
     |
     v
<span style="color:lightblue;">[Navigateur affiche la réponse]</span>
</pre>

