<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<title>Guide Zend Framework 1</title>
<style>
    body {
        font-family: Arial, sans-serif;
        background-color: #f4f6f8;
        margin: 0;
        padding: 20px;
        line-height: 1.6;
    }
    h1, h2 {
        color: #2c3e50;
    }
    h1 {
        border-bottom: 3px solid #3498db;
        padding-bottom: 5px;
    }
    h2 {
        margin-top: 30px;
        color: #2980b9;
    }
    pre {
        background-color: #2d2d2d;
        color: #f8f8f2;
        padding: 15px;
        border-radius: 5px;
        overflow-x: auto;
    }
    code {
        font-family: monospace;
    }
    .note {
        background-color: #ecf0f1;
        border-left: 4px solid #3498db;
        padding: 10px;
        margin: 15px 0;
    }
</style>
</head>
<body>

<h1>📚 Guide rapide Zend Framework 1 — Fonctions essentielles</h1>

<h2>1. Gestion de la base de données : <code>Zend_Db</code> et <code>Zend_Db_Table</code></h2>

<div class="note">Connexion à la base et requêtes simples</div>
<pre><code>// Connexion PDO MySQL
$db = Zend_Db::factory('Pdo_Mysql', array(
    'host'     =&gt; 'localhost',
    'username' =&gt; 'root',
    'password' =&gt; '',
    'dbname'   =&gt; 'ma_base'
));

// Requête simple
$result = $db-&gt;fetchAll('SELECT * FROM articles WHERE actif = ?', 1);
</code></pre>

<div class="note">Modèle basé sur <code>Zend_Db_Table_Abstract</code></div>
<pre><code>class Application_Model_Article extends Zend_Db_Table_Abstract
{
    protected $_name = 'articles';

    public function getAll()
    {
        return $this-&gt;fetchAll()-&gt;toArray();
    }

    public function getById($id)
    {
        return $this-&gt;find($id)-&gt;current()-&gt;toArray();
    }
}
</code></pre>

<h2>2. Contrôleur et gestion des requêtes : <code>Zend_Controller_Action</code></h2>
<pre><code>class ArticleController extends Zend_Controller_Action
{
    public function indexAction()
    {
        $model = new Application_Model_Article();
        $this-&gt;view-&gt;articles = $model-&gt;getAll();
    }

    public function voirAction()
    {
        $id = $this-&gt;_getParam('id', 1);
        $model = new Application_Model_Article();
        $this-&gt;view-&gt;article = $model-&gt;getById($id);
    }
}
</code></pre>

<h2>3. Génération de réponses JSON</h2>
<pre><code>public function listeAction()
{
    $model = new Application_Model_Article();
    $articles = $model-&gt;getAll();

    // Renvoie directement un JSON
    $this-&gt;_helper-&gt;json($articles);
}
</code></pre>

<h2>4. Formulaires : <code>Zend_Form</code></h2>
<pre><code>class Application_Form_Contact extends Zend_Form
{
    public function init()
    {
        $this-&gt;setMethod('post');
                    % type de champ, attribut name dans le HTML
        $this-&gt;addElement('text', 'nom', array(
            'label'    =&gt; 'Nom',
            'required' =&gt; true,
            'filters'  =&gt; array('StringTrim'),
        ));

        $this-&gt;addElement('submit', 'envoyer', array(
            'ignore' =&gt; true,
            'label'  =&gt; 'Envoyer'
        ));
    }
}

<h3>Autres type des champs</h3>
'text' → Zend_Form_Element_Text (champ input classique)
'password' → Zend_Form_Element_Password
'textarea' → Zend_Form_Element_Textarea
'select' → Zend_Form_Element_Select
'checkbox' → Zend_Form_Element_Checkbox
etc.

<h4>Autres type des champs</h4>
Donc ici : 'text' dit à Zend : crée un champ texte (input type="text").
🔹 'username'

👉 C’est le nom de l’élément (= attribut name dans le HTML).
Exemple rendu en HTML :
input type="text" name="username" id="username">
⚠️ Ce name est aussi la clé pour récupérer la valeur après soumission du formulaire.
Exemple :

if ($form->isValid($_POST)) {
    $valeur = $form->getValue('username');
}

</code></pre>

<h2>5. Authentification : <code>Zend_Auth</code></h2>
<pre><code>$authAdapter = new Zend_Auth_Adapter_DbTable($db, 'utilisateurs', 'email', 'mot_de_passe', 'MD5(?)');

$authAdapter-&gt;setIdentity($email)
            -&gt;setCredential($motDePasse);

$result = Zend_Auth::getInstance()-&gt;authenticate($authAdapter);

if ($result-&gt;isValid()) {
    Zend_Auth::getInstance()-&gt;getStorage()-&gt;write($authAdapter-&gt;getResultRowObject());
} else {
    echo "Échec de l'authentification";
}
</code></pre>

<h2>6. Envoi d’e-mails : <code>Zend_Mail</code></h2>
<pre><code>$mail = new Zend_Mail('UTF-8');
$mail-&gt;setBodyText('Bonjour, ceci est un test.')
     -&gt;setFrom('moi@example.com', 'Moi')
     -&gt;addTo('toi@example.com', 'Toi')
     -&gt;setSubject('Sujet du mail')
     -&gt;send();
</code></pre>

<h2>7. Sessions : <code>Zend_Session</code></h2>
<pre><code>Zend_Session::start();
$session = new Zend_Session_Namespace('user');

$session-&gt;nom = 'Jean Dupont';
echo $session-&gt;nom; // Jean Dupont
</code></pre>

<h2>8. Filtrage et validation : <code>Zend_Filter</code> et <code>Zend_Validate</code></h2>
<pre><code>$filter = new Zend_Filter_StripTags();
$clean = $filter-&gt;filter('&lt;b&gt;Texte&lt;/b&gt;'); // "Texte"

$validator = new Zend_Validate_EmailAddress();
if ($validator-&gt;isValid('test@example.com')) {
    echo "Email valide";
} else {
    echo "Email invalide";
}
</code></pre>

<div class="note">
📌 <strong>Résumé des classes clés</strong> :  
<ul>
    <li><strong>Zend_Db</strong> / <strong>Zend_Db_Table_Abstract</strong> → Base de données</li>
    <li><strong>Zend_Controller_Action</strong> → Contrôleurs</li>
    <li><strong>Zend_Form</strong> → Formulaires</li>
    <li><strong>Zend_Auth</strong> → Authentification</li>
    <li><strong>Zend_Mail</strong> → Envoi d’e-mails</li>
    <li><strong>Zend_Session</strong> → Sessions</li>
    <li><strong>Zend_Filter</strong> / <strong>Zend_Validate</strong> → Nettoyage & validation</li>
</ul>
</div>

</body>
</html>
