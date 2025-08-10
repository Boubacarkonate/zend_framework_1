# 14. Fonctionnalités les plus courantes côté controller dans Zend Framework 1

| Fonctionnalité                           | Description                                                       | Exemple rapide                                              |
|----------------------------------------|-------------------------------------------------------------------|-------------------------------------------------------------|
| `$this->_helper->redirector()`         | Redirige vers une autre action / contrôleur                      | `$this->_helper->redirector('index', 'article');`           |
| `$this->getRequest()`                   | Récupère l’objet requête HTTP (GET, POST, params, etc.)           | `$request = $this->getRequest();`                            |
| `$this->getRequest()->isPost()`        | Vérifie si la requête est POST                                    | `if ($this->getRequest()->isPost()) { ... }`                 |
| `$this->getRequest()->getPost()`       | Récupère un paramètre POST                                        | `$val = $this->getRequest()->getPost('key');`                |
| `$this->getRequest()->getParam()`      | Récupère un paramètre GET ou POST                                 | `$val = $this->getRequest()->getParam('id', 0);`             |
| `$this->_helper->flashMessenger`       | Ajouter / récupérer des messages flash (stockés en session)      | `$this->_helper->flashMessenger->addMessage('Success');`    |
| `$this->_helper->viewRenderer`         | Contrôle l’affichage de la vue (désactiver, changer la vue)       | `$this->_helper->viewRenderer->setNoRender(true);`          |
| `$this->_helper->layout`                | Activer/désactiver ou changer le layout (mise en page globale)    | `$this->_helper->layout->disableLayout();`                   |
| `$this->_helper->json()`                | Retourne une réponse JSON facilement                              | `$this->_helper->json(['success' => true]);`                 |
| `$this->_helper->getHelper()`           | Récupère un helper d’action spécifique                            | `$redirector = $this->_helper->getHelper('Redirector');`     |
| `$this->_forward()`                     | Transfert le contrôle vers une autre action sans redirection HTTP | `$this->_forward('list', 'article');`                         |
| `$this->_helper->contextSwitch()`      | Gérer plusieurs formats (json, xml, html)                         | `$this->_helper->contextSwitch()->initContext();`            |
| `$this->_helper->layout->setLayout()`  | Change le layout utilisé                                          | `$this->_helper->layout->setLayout('admin');`                |
| `$this->getResponse()`                  | Manipule l’objet réponse HTTP (headers, corps, etc.)             | `$this->getResponse()->setHeader('Content-Type', 'application/json');` |
| `throw new Exception()`                 | Lancer une exception pour gérer les erreurs                      | `throw new Exception('Erreur');`                              |
| `$this->view`                          | Passage de variables à la vue                                     | `$this->view->variable = $data;`                             |
| `$this->_helper->getHelper('FlashMessenger')->addMessage()` | Ajouter un message flash                                           | `$this->_helper->getHelper('FlashMessenger')->addMessage('ok');` |
| `$this->_helper->AjaxContext()`        | Gérer requêtes Ajax avec différents formats                       | `$this->_helper->AjaxContext()->addActionContext('index', 'json')->initContext();` |
| `$this->_helper->layout->disableLayout()` | Désactive le layout                                              | `$this->_helper->layout->disableLayout();`                   |
| `$this->_helper->viewRenderer->setNoRender(true)` | Désactive le rendu de la vue                                     | `$this->_helper->viewRenderer->setNoRender(true);`           |
| `$this->_helper->redirector->gotoSimple()` | Redirige vers une route simple                                  | `$this->_helper->redirector->gotoSimple('index', 'article');`|
| `$this->_helper->getHelper('AjaxContext')` | Pour gérer la réponse Ajax avec contextes variés                 | `$this->_helper->getHelper('AjaxContext')->initContext();`   |
| `$this->_helper->getHelper('Json')`    | Pour retourner du JSON                                            | `$this->_helper->getHelper('Json')->direct($data);`          |
| `$this->_helper->getHelper('ViewRenderer')->renderScript()` | Rendre un script de vue spécifique                               | `$this->_helper->viewRenderer->renderScript('mon-script.phtml');` |
| `$this->_helper->getHelper('Layout')->enableLayout()` | Activer le layout                                               | `$this->_helper->layout->enableLayout();`                    |
| `$this->_helper->getHelper('Layout')->disableLayout()` | Désactiver le layout                                            | `$this->_helper->layout->disableLayout();`                   |
| `$this->_helper->getHelper('ActionStack')` | Empiler des actions pour exécution dans la même requête          | `$this->_helper->getHelper('ActionStack')->pushStack('index', 'article');` |
| `$this->_helper->getHelper('ViewRenderer')->setViewSuffix()` | Changer l’extension des vues                                   | `$this->_helper->viewRenderer->setViewSuffix('tpl');`       |
| `$this->_helper->getHelper('Redirector')->gotoRoute()` | Redirection vers une route avec paramètres                      | `$this->_helper->redirector->gotoRoute(['controller'=>'index'], 'default');` |
| `$this->_helper->getHelper('Json')->sendJson()` | Envoyer JSON et stopper l’exécution                            | `$this->_helper->json->sendJson(['status'=>'ok']);`          |
| `$this->_helper->getHelper('FlashMessenger')->getMessages()` | Récupérer les messages flash stockés                            | `$messages = $this->_helper->flashMessenger->getMessages();` |
| `$this->_helper->getHelper('FlashMessenger')->hasMessages()` | Vérifier s’il y a des messages flash                            | `if ($this->_helper->flashMessenger->hasMessages()) { ... }` |

---

### Notes complémentaires

- La plupart des helpers s’utilisent via `$this->_helper->nomDuHelper`
- `$this->getRequest()` permet d’accéder à toutes les infos de la requête HTTP
- Le système FlashMessenger stocke les messages entre requêtes via session
- Le `viewRenderer` et le `layout` contrôlent la partie présentation (templates)
- `redirector` permet toutes sortes de redirections simples ou complexes
- Le `contextSwitch` permet d’adapter la sortie selon le format demandé (json, xml, etc.)
- La gestion des exceptions est centrale pour afficher ou logguer les erreurs

---

## Exemple dans un controller
```php
<?php
class ArticleController extends Zend_Controller_Action
{
    public function init()
    {
        // Initialisation du layout
        $this->_helper->layout->setLayout('main');

        // Initialiser contextSwitch pour gérer json et html sur certaines actions
        $this->_helper->contextSwitch()
            ->addActionContext('list', ['json', 'html'])
            ->addActionContext('view', ['json', 'html'])
            ->initContext();

        // Exemple d’utilisation FlashMessenger : récupérer messages flash dans la vue
        $this->view->messages = $this->_helper->flashMessenger->getMessages();
    }

    // Liste des articles (html ou json selon contexte)
    public function listAction()
    {
        // Exemple : récupérer des paramètres GET/POST
        $page = $this->getRequest()->getParam('page', 1);

        // Exemple simple d’utilisation modèle (mocké ici)
        $articles = [
            ['id'=>1, 'titre'=>'Article 1', 'contenu'=>'Lorem ipsum...'],
            ['id'=>2, 'titre'=>'Article 2', 'contenu'=>'Dolor sit amet...']
        ];

        // Passer les données à la vue
        $this->view->articles = $articles;
        $this->view->page = $page;

        // Si contexte JSON (ajax), on retourne les données en JSON
        if ($this->_helper->contextSwitch()->getCurrentContext() == 'json') {
            $this->_helper->json($articles);
        }
    }

    // Voir un article (html ou json)
    public function viewAction()
    {
        // Récupérer paramètre id (GET)
        $id = (int) $this->getRequest()->getParam('id', 0);

        if ($id <= 0) {
            // Utiliser flash messenger pour message d'erreur
            $this->_helper->flashMessenger->addMessage('ID invalide');
            // Redirection vers la liste
            return $this->_helper->redirector('list');
        }

        // Simuler récupération article depuis modèle
        $article = ['id'=>$id, 'titre'=>"Article #$id", 'contenu'=>"Contenu de l'article $id"];

        // Passer article à la vue
        $this->view->article = $article;

        // Context JSON
        if ($this->_helper->contextSwitch()->getCurrentContext() == 'json') {
            $this->_helper->json($article);
        }
    }

    // Ajouter un article (formulaire)
    public function addAction()
    {
        $request = $this->getRequest();

        if ($request->isPost()) {
            // Récupérer les données POST
            $titre = $request->getPost('titre', '');
            $contenu = $request->getPost('contenu', '');

            // Validation simple (à améliorer avec Zend_Form ou Zend_Filter_Input)
            if (empty($titre) || empty($contenu)) {
                $this->_helper->flashMessenger->addMessage('Titre et contenu obligatoires');
                // On reste sur la même page (vue form)
                return;
            }

            // Ici on insérerait en base via modèle (simulé)
            // $model->insert(['titre'=>$titre, 'contenu'=>$contenu]);

            // Message succès flash
            $this->_helper->flashMessenger->addMessage('Article ajouté avec succès');

            // Redirection vers liste
            return $this->_helper->redirector('list');
        }

        // Sinon afficher formulaire
        // Variables passées automatiquement à la vue via $this->view
    }

    // Action pour démontrer disableLayout et noRender
    public function rawdataAction()
    {
        // Désactiver layout et vue : réponse brute
        $this->_helper->layout->disableLayout();
        $this->_helper->viewRenderer->setNoRender(true);

        // Envoyer header
        $this->getResponse()->setHeader('Content-Type', 'text/plain');

        echo "Ceci est une réponse brute, sans layout ni vue.";
    }

    // Action qui utilise forward (redirection interne sans changement URL)
    public function forwardAction()
    {
        // Transférer vers viewAction sans redirection HTTP
        $this->_forward('view');
    }

    // Action qui illustre envoi JSON avec helper JSON
    public function apiAction()
    {
        $data = ['status'=>'ok', 'timestamp'=>time()];
        $this->_helper->json($data);
    }

    // Action pour montrer ajout message flash et redirection simple
    public function flashAction()
    {
        $this->_helper->flashMessenger->addMessage('Ceci est un message flash.');
        $this->_helper->redirector('list');
    }
}



