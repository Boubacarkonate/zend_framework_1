# 10. Les données Json 

## Différence entre `$this->_helper->json()` et `json_encode()` + `$this->view`

### 1. `$this->_helper->json($data)`
- **But** : Envoyer directement du JSON au client (navigateur, application, etc.).
- **Fonctionnement** :
  - Sérialise `$data` en JSON.
  - Envoie un en-tête HTTP `Content-Type: application/json`.
  - Arrête immédiatement le traitement **avant** le rendu de la vue.
- **Usage typique** :
  - APIs REST.
  - Réponses AJAX où l’on veut **uniquement** des données JSON (pas de HTML).
- **Avantages** :
  - Rapide, direct, optimisé.
  - Pas besoin de gérer l’encodage JSON soi-même.
- **Inconvénients** :
  - Impossible d’afficher en même temps du HTML et du JSON dans la même requête.
  - Pas de passage par la vue (on perd la mise en forme et les helpers de la vue).

---

### 2. `json_encode($data)` + `$this->view->variable`
- **But** : Fournir des données JSON **à l’intérieur** d’une vue HTML.
- **Fonctionnement** :
  - On encode manuellement `$data` avec `json_encode()`.
  - On transmet la chaîne JSON à une variable de vue (`$this->view->variable`).
  - La vue peut alors afficher du HTML **et** du JSON, ou insérer les données dans du JavaScript.
- **Usage typique** :
  - Pages HTML dynamiques qui doivent initialiser du JavaScript avec des données serveur.
  - Mélanger rendu côté serveur (PHP/Zend) et rendu côté client (JS).
- **Avantages** :
  - Grande flexibilité (HTML + JSON dans une même réponse).
  - Possibilité de styliser et préparer les données avant leur utilisation côté client.
- **Inconvénients** :
  - Nécessite un encodage et une gestion manuelle du JSON.
  - Légèrement moins performant que `_helper->json()` si on veut juste des données brutes.

---

### 📌 En résumé
| Méthode                         | Type de sortie        | Vue utilisée ? | Usage recommandé |
|---------------------------------|-----------------------|----------------|------------------|
| `$this->_helper->json($data)`   | JSON pur              | ❌ Non         | API, AJAX pur   |
| `json_encode()` + `$this->view` | HTML + JSON intégré   | ✅ Oui         | Pages dynamiques avec JS |


## Exemple concret : Réponse JSON dans un contrôleur Zend

---

## 1. Avec `$this->_helper->json()`
Ici, la sortie est **uniquement du JSON**.  
Aucune vue n’est utilisée, idéal pour une API ou une requête AJAX qui ne veut que des données.

### Contrôleur
```php
class ArticleController extends Zend_Controller_Action
{
    public function lireJsonAction()
    {
        $id = $this->_getParam('id', 1);

        $articleModel = new Application_Model_Article();
        $article = $articleModel->getArticleById($id);

        // Envoi direct du JSON
        $this->_helper->json($article);
    }
}

```
### Résultat dans le navigateur (URL : /article/lire-json/id/1)
```json
{
    "id": 1,
    "titre": "Mon premier article",
    "contenu": "Ceci est le contenu...",
    "date_creation": "2025-08-09"
}
Aucun HTML, juste du JSON brut.
```
---
## 2. Avec json_encode() + $this->view
Ici, on encode les données en JSON et on les passe à la vue,
permettant d'afficher à la fois du HTML et du JSON.



### Contrôleur
```php
class ArticleController extends Zend_Controller_Action
{
    public function lireAction()
    {
        $id = $this->_getParam('id', 1);

        $articleModel = new Application_Model_Article();
        $article = $articleModel->getArticleById($id);

        // On garde l'article en tableau PHP pour la vue
        $this->view->article = $article;

        // On prépare aussi sa version JSON pour un script JS
        $this->view->articleJson = json_encode($article);
    }
}


```
### Vue (lire.phtml)

```html
<h1><?= $this->escape($this->article['titre']); ?></h1>
<p><?= nl2br($this->escape($this->article['contenu'])); ?></p>
<small>Publié le : <?= $this->escape($this->article['date_creation']); ?></small>

<!-- Données JSON intégrées pour JavaScript -->
<script>
    const articleData = <?= $this->articleJson ?>;
    console.log("Article depuis PHP :", articleData);
</script>
```
Ici, la page HTML est affichée normalement, et les données JSON sont prêtes à être utilisées côté JavaScript.

---
## 3. Récupérer des données JSON via AJAX en PHP/Zend
- Contrôleur JSON → renvoie uniquement des données (pas de HTML).
- AJAX → récupère ces données côté client sans recharger la page.
- jQuery simplifie la requête AJAX et l’insertion dans le DOM.



### Contrôleur côté serveur

```php
class ArticleController extends Zend_Controller_Action
{
    public function listeJsonAction()
    {
        // Désactiver la vue et le layout
        $this->_helper->viewRenderer->setNoRender();
        $this->_helper->layout->disableLayout();

        // Exemple de données (normalement récupérées depuis le modèle)
        $articles = [
            ['id' => 1, 'titre' => 'Article 1', 'date' => '2025-08-09'],
            ['id' => 2, 'titre' => 'Article 2', 'date' => '2025-08-08'],
            ['id' => 3, 'titre' => 'Article 3', 'date' => '2025-08-07'],
        ];

        // Réponse JSON
        $this->_helper->json($articles);
    }
}

```
### L'action du controller renverra uniquement du JSON comme :
```json
[
    {"id":1,"titre":"Article 1","date":"2025-08-09"},
    {"id":2,"titre":"Article 2","date":"2025-08-08"},
    {"id":3,"titre":"Article 3","date":"2025-08-07"}
]

```
### Vue HTML avec AJAX (index.phtml)

```html
<h1>Liste des articles</h1>
<ul id="articles"></ul>

<script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
<script>
// Appel AJAX vers l'action JSON
$.ajax({
    url: '/article/liste-json', // URL de l'action Zend
    method: 'GET',
    dataType: 'json',
    success: function(data) {
        let html = '';
        data.forEach(article => {
            html += `<li>${article.titre} - ${article.date}</li>`;
        });
        $('#articles').html(html);
    },
    error: function() {
        alert('Erreur lors de la récupération des articles.');
    }
});
</script>

```
Quand la page se charge, jQuery appelle /article/liste-json,
récupère les données JSON, puis les insère dans la liste HTML.

### Résultat visuel dans le navigateur

```css
Liste des articles
• Article 1 - 2025-08-09
• Article 2 - 2025-08-08
• Article 3 - 2025-08-07
