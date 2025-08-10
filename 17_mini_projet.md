# 15. Mini-projet Zend Framework 1 : gestion d’articles

## 1. Structure simplifiée

```textplain
/application
    /models
        Article.php
    /controllers
        ArticleController.php
    /views
        /scripts
            /article
                index.phtml
                add.phtml
/public
    index.php
    .htaccess
/application
    Bootstrap.php
/application
    application.ini

```

## 2. Fichier /application/models/Article.php
```php
<?php
class Application_Model_Article extends Zend_Db_Table_Abstract
{
    protected $_name = 'articles'; // nom de la table

    public function fetchAllArticles()
    {
        return $this->fetchAll()->toArray();
    }

    public function addArticle($titre, $contenu)
    {
        $data = [
            'titre' => $titre,
            'contenu' => $contenu,
            'date_creation' => date('Y-m-d H:i:s'),
            'actif' => 1
        ];
        return $this->insert($data);
    }
}

```
## 3. Fichier /application/controllers/ArticleController.php
```php
<?php
class ArticleController extends Zend_Controller_Action
{
    protected $model;

    public function init()
    {
        $this->model = new Application_Model_Article();
    }

    // Affiche la liste des articles
    public function indexAction()
    {
        $this->view->articles = $this->model->fetchAllArticles();
    }

    // Formulaire d'ajout d'article
    public function addAction()
    {
        if ($this->getRequest()->isPost()) {
            $titre = $this->getRequest()->getPost('titre');
            $contenu = $this->getRequest()->getPost('contenu');

            if ($titre && $contenu) {
                $this->model->addArticle($titre, $contenu);
                // Redirection vers la liste
                return $this->_helper->redirector('index');
            } else {
                $this->view->error = "Tous les champs sont obligatoires.";
            }
        }
    }
}

```
## 4. Vue /application/views/scripts/article/index.phtml
```php
<h1>Liste des articles</h1>

<a href="<?php echo $this->url(['controller'=>'article', 'action'=>'add']); ?>">Ajouter un article</a>

<ul>
    <?php foreach ($this->articles as $article): ?>
        <li>
            <strong><?php echo $this->escape($article['titre']); ?></strong> — 
            <em><?php echo date('d/m/Y H:i', strtotime($article['date_creation'])); ?></em><br>
            <?php echo nl2br($this->escape($article['contenu'])); ?>
        </li>
    <?php endforeach; ?>
</ul>

```

## 5. Vue /application/views/scripts/article/add.phtml
```php
<h1>Liste des articles</h1>

<a href="<?php echo $this->url(['controller'=>'article', 'action'=>'add']); ?>">Ajouter un article</a>

<ul>
    <?php foreach ($this->articles as $article): ?>
        <li>
            <strong><?php echo $this->escape($article['titre']); ?></strong> — 
            <em><?php echo date('d/m/Y H:i', strtotime($article['date_creation'])); ?></em><br>
            <?php echo nl2br($this->escape($article['contenu'])); ?>
        </li>
    <?php endforeach; ?>
</ul>

```