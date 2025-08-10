# 8. Exemple de Controller : `ArticleController.php`

Le contrôleur reçoit la requête, récupère les paramètres, interagit avec le modèle, prépare la vue ou envoie du JSON.

```php
<?php
class ArticleController extends Zend_Controller_Action
{
    public function lireAction()
    {
        // Récupère l'id passé en paramètre, 1 par défaut
        $id = $this->_getParam('id', 1);
        
        // Instancie le modèle Article
        $articleModel = new Application_Model_Article();
        
        // Récupère l'article depuis la base de données
        $article = $articleModel->getArticleById($id);
        
        // Passe les données à la vue
        $this->view->article = $article;

        // Si le paramètre format=json est présent, retourne la réponse en JSON
        if ($this->_getParam('format') === 'json') {
            $this->_helper->json($article);
        }
    }
}
