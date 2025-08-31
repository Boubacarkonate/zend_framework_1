# Les requêtes SQL 

## 📑 Tableau comparatif — Zend_Db_Select ↔ SQL

| Méthode Zend_Db_Select | Équivalent SQL | Exemple |
|---|---|---|
| `from('table')` | `SELECT * FROM table` | ```php\n$select->from('articles');\n``` |
| `from('table', array('col1','col2'))` | `SELECT col1, col2 FROM table` | ```php\n$select->from('articles', ['id','titre']);\n``` |
| `columns(array(...))` | `SELECT col1, col2 (sur table déjà définie)` | ```php\n$select->from('articles')->columns(['id','titre']);\n``` |
| `where('col = ?', $val)` | `WHERE col = 'val'` | ```php\n$select->where('actif = ?', 1);\n``` |
| `orWhere('col = ?', $val)` | `OR col = 'val'` | ```php\n$select->where('actif = ?', 1)->orWhere('actif = ?', 2);\n``` |
| `order('col DESC')` | `ORDER BY col DESC` | ```php\n$select->order('date_creation DESC');\n``` |
| `limit(10)` | `LIMIT 10` | ```php\n$select->limit(10);\n``` |
| `limit(10, 5)` | `LIMIT 10 OFFSET 5` | ```php\n$select->limit(10, 5);\n``` |
| `group('col')` | `GROUP BY col` | ```php\n$select->group('categorie_id');\n``` |
| `having('COUNT(col) > ?', 1)` | `HAVING COUNT(col) > 1` | ```php\n$select->group('categorie_id')->having('COUNT(id) > ?', 1);\n``` |
| `join(array('alias'=>'table'), 'cond', array('col1','col2'))` | `JOIN table alias ON cond` | ```php\n$select->join(['u'=>'utilisateurs'], 'a.auteur_id = u.id', ['u.nom']);\n``` |
| `joinLeft(...)` | `LEFT JOIN` | ```php\n$select->joinLeft(['u'=>'utilisateurs'], 'a.auteur_id = u.id', ['u.nom']);\n``` |
| `joinRight(...)` | `RIGHT JOIN` | ```php\n$select->joinRight(['u'=>'utilisateurs'], 'a.auteur_id = u.id', ['u.nom']);\n``` |
| `distinct()` | `SELECT DISTINCT` | ```php\n$select->distinct();\n``` |
| `union(array($sel1, $sel2))` | `SELECT ... UNION SELECT ...` | ```php\n$select = $db->select()->union([$sel1, $sel2]);\n``` |
| `query()` | Exécute la requête et retourne un objet statement | ```php\n$stmt = $select->query();\n``` |
| `$db->fetchAll($select)` | Exécute et retourne un tableau | ```php\n$result = $db->fetchAll($select);\n``` |
| `$db->fetchRow($select)` | Exécute et retourne une seule ligne | ```php\n$row = $db->fetchRow($select);\n``` |

---

## 📌 Exemple complet combinant plusieurs méthodes

```php
<?php
class Application_Model_Article extends Zend_Db_Table_Abstract
{
    protected $_name = 'articles';  // Nom de la table principale

    /**
     * Récupère les articles actifs, avec auteur, filtrés, groupés et triés
     * @return array Résultats sous forme de tableau associatif
     */
    public function getArticles_and_stats_byAuthor()
    {
        $select = $this->select()
                       ->setIntegrityCheck(false) // Nécessaire pour joindre d'autres tables
                       ->distinct()
                       ->from(['a' => $this->_name], ['id', 'titre', 'date_creation'])
                       ->joinLeft(
                           ['u' => 'utilisateurs'],
                           'a.auteur_id = u.id',
                           ['auteur' => 'u.nom']
                       )
                       ->where('a.actif = ?', 1)     //Chaque where() s’ajoute avec un AND par défaut.
                       ->where('a.date_creation > ?', '2025-01-01')
                       ->group('a.categorie_id')
                       ->having('COUNT(a.id) > ?', 2)
                       ->order('a.date_creation DESC')
                       ->limit(10);

        return $this->fetchAll($select)->toArray();
    }
}
```
## 📑 Opérations CRUD rapides avec Zend_Db et Zend_Db_Table_Abstract

| Méthode ZF1 | Équivalent SQL | Exemple |
|---|---|---|
| `$db->insert('table', $data)` | `INSERT INTO table (col1, col2, ...) VALUES (val1, val2, ...)` | ```php\n$db->insert('articles', [ 'titre' => 'Mon titre', 'contenu' => 'Texte...', 'actif' => 1 ]);\n``` |
| `$db->update('table', $data, $where)` | `UPDATE table SET col1 = val1, col2 = val2 WHERE condition` | ```php\n$db->update('articles', [ 'titre' => 'Titre modifié' ], "id = 5");\n``` |
| `$db->delete('table', $where)` | `DELETE FROM table WHERE condition` | ```php\n$db->delete('articles', "id = 5");\n``` |

---

## 📌 Exemple complet avec INSERT, UPDATE, DELETE

```php
class Application_Model_Article extends Zend_Db_Table_Abstract
{
    protected $_name = 'articles';
}

$model = new Application_Model_Article();

// INSERT
$model->insert([
    'titre'   => 'Titre depuis modèle',
    'contenu' => 'Texte inséré via modèle...',
    'actif'   => 1
]);

// UPDATE
$model->update(
    ['titre' => 'Titre mis à jour via modèle'],
    "id = 3"
);

// DELETE
$model->delete("id = 4");
```
## Les principales propriétés de Zend_Db_Table_Abstract
- 1. protected $_name
 -     protected $_name = 'users';        // obligatoire si le nom n'est pas devinable
Nom de la table SQL (on l’a vu).

- 2. protected $_primary
 -  protected $_primary = 'user_id';   // obligatoire si != "id"
Clé(s) primaire(s) de la table.
➡️ Peut être une chaîne ('id') ou un tableau (si ta table a une clé primaire composée).

- 3. protected $_schema
 - protected $_schema = 'zf1_crud';
Nom du schéma ou de la base de données si ce n’est pas la base par défaut.

- 4. protected $_sequence
 - protected $_sequence = false;
Indique si la clé primaire est auto-incrémentée (true par défaut).
Si ta clé primaire n’est pas auto-incrémentée, tu peux la passer à false.