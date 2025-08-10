# Création d’un layout simple avec Zend Framework 1 (ZF1)

Le layout permet d’encapsuler toutes les vues dans un gabarit commun (header, footer, menu, etc.).

---

## 1. Crée le dossier pour les layouts

Dans `application/layouts/scripts/` crée un fichier `layout.phtml`.

```makefile
C:\wamp64\www\zf1-manuel\application\layouts\scripts\layout.phtml
```

## 2. Exemple de contenu du fichier layout.phtml
```phtml
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8" />
    <title><?php echo $this->escape($this->title ?: 'Mon Application ZF1'); ?></title>
    <link rel="stylesheet" href="/css/style.css" />
</head>
<body>
    <header>
        <h1>Mon Application ZF1</h1>
        <nav>
            <a href="/">Accueil</a> |
            <a href="/index">Index</a>
        </nav>
        <hr />
    </header>

    <section id="content">
        <?php echo $this->layout()->content; ?>
    </section>

    <footer>
        <hr />
        <p>&copy; <?php echo date('Y'); ?> - Mon Application</p>
    </footer>
</body>
</html>
```

## 3. Activer le layout dans Bootstrap.php
Dans application/Bootstrap.php, ajoute la méthode pour initialiser le layout :
```php
<?php
class Bootstrap extends Zend_Application_Bootstrap_Bootstrap
{
    protected function _initLayout()
    {
        // Active le layout et définit le chemin et le nom du layout
        Zend_Layout::startMvc([
            'layoutPath' => APPLICATION_PATH . '/layouts/scripts',
            'layout'     => 'layout',
        ]);
    }
}
```
## 4. Optionnel : Modifier le contrôleur pour utiliser le layout
Normalement, Zend_Layout s’active automatiquement, mais on peut forcer dans un contrôleur :
```php
public function init()
{
    $this->_helper->layout->enableLayout();
}
```

## 5. Résultat
Toutes tes vues seront rendues à l’intérieur du layout, dans la section :

```php
<?php echo $this->layout()->content; ?>
```
On peut personnaliser le layout (ajouter CSS, menus, scripts, footer, etc.) pour avoir une structure commune à toutes les pages.

