# 7. Exemple de View : `lire.phtml`

La vue affiche proprement les données avec **_<php $this->variableInitialisé ?>_**, en protégeant contre les injections via _$this->escape()_. I

```html
<?php if (isset($this->articleJson)): ?>
    <script>
        // Récupération et utilisation des données JSON en JavaScript
        const articleData = JSON.parse('<?= $this->escape($this->articleJson); ?>');
        console.log(articleData); // Exploitation des données côté client
    </script>
<?php else: ?>
    <h1><?= $this->escape($this->article['titre']); ?></h1>
    <p><?= nl2br($this->escape($this->article['contenu'])); ?></p>
    <small>Publié le : <?= $this->escape($this->article['date_creation']); ?></small>
<?php endif; ?>



