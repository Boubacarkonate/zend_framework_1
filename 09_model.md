# 7. Exemple de Model : `Article.php`

Le modèle encapsule la logique d’accès à la base de données via Zend_Db_Table_Abstract.

```php
<?php
class Application_Model_Article extends Zend_Db_Table_Abstract
{
    // Nom de la table dans la base
    protected $_name = 'articles';

    // Récupère un article par son ID
    public function getArticleById($id)
    {
        $row = $this->find($id)->current();
        if (!$row) {
            throw new Exception("Article non trouvé");
        }
        return $row->toArray();
    }
}

