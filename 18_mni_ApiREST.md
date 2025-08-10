# Mini projet API REST

## Api.php
```php
<?php
class Application_Model_ApiModel
{
    // URL de base de l'API à interroger (par défaut JSONPlaceholder)
    protected $apiBaseUrl;

    /**
     * Constructeur, permet de définir l'URL de base de l'API
     * @param string|null $apiBaseUrl URL de base, sinon la valeur par défaut est utilisée
     */
    public function __construct($apiBaseUrl = null)
    {
        $this->apiBaseUrl = $apiBaseUrl ?: 'https://jsonplaceholder.typicode.com';
    }

    /**
     * Méthode générique pour effectuer une requête HTTP avec cURL
     *
     * @param string $endpoint URL relative à l'API (ex : '/posts/1')
     * @param string $method Méthode HTTP à utiliser (GET, POST, PUT, DELETE)
     * @param array|null $data Données à envoyer en POST ou PUT (tableau associatif)
     * @return array|null Retourne le tableau décodé JSON ou null si erreur cURL
     */
    protected function request($endpoint, $method = 'GET', $data = null)
    {
        // Construction de l'URL complète
        $url = $this->apiBaseUrl . $endpoint;

        // Initialisation de la session cURL
        $ch = curl_init($url);

        // Retourner la réponse au lieu de l'afficher directement
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);

        // Indique qu'on attend une réponse au format JSON
        curl_setopt($ch, CURLOPT_HTTPHEADER, ['Accept: application/json']);

        // Définition du comportement selon la méthode HTTP
        switch (strtoupper($method)) {
            case 'POST':
                // Passage en POST
                curl_setopt($ch, CURLOPT_POST, true);

                if ($data !== null) {
                    // Envoi des données encodées en JSON
                    curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($data));

                    // Modification des headers pour préciser le type de contenu JSON
                    curl_setopt($ch, CURLOPT_HTTPHEADER(), [
                        'Content-Type: application/json',
                        'Accept: application/json'
                    ]);
                }
                break;

            case 'PUT':
                // Utilisation d'une requête PUT personnalisée
                curl_setopt($ch, CURLOPT_CUSTOMREQUEST, 'PUT');

                if ($data !== null) {
                    // Envoi des données encodées en JSON
                    curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($data));

                    // Headers pour JSON
                    curl_setopt($ch, CURLOPT_HTTPHEADER(), [
                        'Content-Type: application/json',
                        'Accept: application/json'
                    ]);
                }
                break;

            case 'DELETE':
                // Requête DELETE personnalisée
                curl_setopt($ch, CURLOPT_CUSTOMREQUEST, 'DELETE');
                break;

            case 'GET':
            default:
                // GET par défaut : pas besoin de config supplémentaire
                break;
        }

        // Exécution de la requête et récupération de la réponse
        $response = curl_exec($ch);

        // Gestion des erreurs cURL
        if (curl_errno($ch)) {
            // Fermeture de la session avant retour null
            curl_close($ch);
            return null;
        }

        // Fermeture de la session cURL
        curl_close($ch);

        // Décodage du JSON en tableau associatif PHP
        $decoded = json_decode($response, true);

        // Retour du tableau décodé (ou null si JSON invalide)
        return $decoded;
    }

    /**
     * Récupération des données via GET (méthode simplifiée)
     * @param string $endpoint Chemin relatif à l'API
     * @return array|null Données JSON décodées ou null
     */
    public function fetchData($endpoint)
    {
        return $this->request($endpoint, 'GET');
    }

    /**
     * Récupère tous les posts (GET /posts)
     * @return array|null Liste des posts
     */
    public function getPosts()
    {
        return $this->fetchData('/posts');
    }

    /**
     * Récupère un post précis par son ID (GET /posts/{id})
     * @param int $id ID du post
     * @return array|null Données du post
     */
    public function getPostById($id)
    {
        return $this->fetchData('/posts/' . intval($id));
    }

    /**
     * Crée un nouveau post (POST /posts)
     * @param array $data Données du post (ex: ['title' => ..., 'body' => ..., 'userId' => ...])
     * @return array|null Réponse de l'API
     */
    public function createPost(array $data)
    {
        return $this->request('/posts', 'POST', $data);
    }

    /**
     * Met à jour un post existant (PUT /posts/{id})
     * @param int $id ID du post à mettre à jour
     * @param array $data Données à mettre à jour
     * @return array|null Réponse de l'API
     */
    public function updatePost($id, array $data)
    {
        return $this->request('/posts/' . intval($id), 'PUT', $data);
    }

    /**
     * Supprime un post (DELETE /posts/{id})
     * @param int $id ID du post à supprimer
     * @return array|null Réponse de l'API
     */
    public function deletePost($id)
    {
        return $this->request('/posts/' . intval($id), 'DELETE');
    }
}

```

## ApiController.php
```php
<?php
class ApiController extends Zend_Controller_Action
{
    // Propriété qui contiendra l'instance du modèle ApiModel
    protected $apiModel;

    /**
     * Méthode d'initialisation appelée avant chaque action
     */
    public function init()
    {
        // Instanciation du modèle qui interagit avec l'API
        $this->apiModel = new Application_Model_ApiModel();

        // Optionnel : définir un layout personnalisé (ici 'main')
        $this->_helper->layout->setLayout('main');

        // Récupérer les messages flash pour les afficher dans les vues
        $this->view->messages = $this->_helper->flashMessenger->getMessages();
    }

    /**
     * Action pour afficher la liste des posts
     */
    public function listAction()
    {
        // Récupérer tous les posts via le modèle
        $posts = $this->apiModel->getPosts();

        // Passer les posts à la vue
        $this->view->posts = $posts;
    }

    /**
     * Action pour afficher un post en détail
     */
    public function viewAction()
    {
        // Récupération de l'ID passé en paramètre URL
        $id = (int) $this->getRequest()->getParam('id', 0);

        // Validation simple de l'ID
        if ($id <= 0) {
            $this->_helper->flashMessenger->addMessage('ID invalide');
            return $this->_helper->redirector('list'); // Rediriger vers la liste
        }

        // Récupérer le post via le modèle
        $post = $this->apiModel->getPostById($id);

        if (!$post) {
            $this->_helper->flashMessenger->addMessage('Post non trouvé');
            return $this->_helper->redirector('list'); // Rediriger si post absent
        }

        // Passer le post à la vue
        $this->view->post = $post;
    }

    /**
     * Action pour ajouter un nouveau post (gère la soumission POST)
     */
    public function addAction()
    {
        $request = $this->getRequest();

        // Vérifie si le formulaire a été soumis en POST
        if ($request->isPost()) {
            // Récupération des données du formulaire
            $data = [
                'title'  => $request->getPost('title'),
                'body'   => $request->getPost('body'),
                'userId' => $request->getPost('userId', 1), // valeur par défaut 1
            ];

            // Validation simple : titre et contenu obligatoires
            if (empty($data['title']) || empty($data['body'])) {
                $this->_helper->flashMessenger->addMessage('Titre et contenu obligatoires');
                return; // Stop, on affiche à nouveau le formulaire
            }

            // Appel du modèle pour créer le post via l'API
            $result = $this->apiModel->createPost($data);

            if ($result) {
                // Message succès et redirection vers la liste
                $this->_helper->flashMessenger->addMessage('Post ajouté avec succès');
                return $this->_helper->redirector('list');
            } else {
                // Message d'erreur en cas d'échec
                $this->_helper->flashMessenger->addMessage('Erreur lors de la création');
            }
        }
        // Si pas de POST, la vue affichera simplement le formulaire
    }

    /**
     * Action pour modifier un post existant (affiche le formulaire et traite la soumission)
     */
    public function editAction()
    {
        $request = $this->getRequest();

        // Récupérer l'ID du post à modifier
        $id = (int) $this->getRequest()->getParam('id', 0);

        // Validation simple de l'ID
        if ($id <= 0) {
            $this->_helper->flashMessenger->addMessage('ID invalide');
            return $this->_helper->redirector('list');
        }

        if ($request->isPost()) {
            // Récupération des données modifiées
            $data = [
                'title'  => $request->getPost('title'),
                'body'   => $request->getPost('body'),
                'userId' => $request->getPost('userId', 1),
            ];

            // Validation simple
            if (empty($data['title']) || empty($data['body'])) {
                $this->_helper->flashMessenger->addMessage('Titre et contenu obligatoires');
                return; // On revient sur le formulaire
            }

            // Mise à jour via le modèle
            $result = $this->apiModel->updatePost($id, $data);

            if ($result) {
                $this->_helper->flashMessenger->addMessage('Post modifié avec succès');
                return $this->_helper->redirector('list');
            } else {
                $this->_helper->flashMessenger->addMessage('Erreur lors de la modification');
            }
        } else {
            // Chargement du post pour pré-remplir le formulaire
            $post = $this->apiModel->getPostById($id);

            if (!$post) {
                $this->_helper->flashMessenger->addMessage('Post non trouvé');
                return $this->_helper->redirector('list');
            }

            // Passage des données à la vue pour affichage dans le formulaire
            $this->view->post = $post;
        }
    }

    /**
     * Action pour supprimer un post
     */
    public function deleteAction()
    {
        // Récupérer l'ID à supprimer
        $id = (int) $this->getRequest()->getParam('id', 0);

        if ($id <= 0) {
            $this->_helper->flashMessenger->addMessage('ID invalide');
            return $this->_helper->redirector('list');
        }

        // Appel du modèle pour supprimer via API
        $result = $this->apiModel->deletePost($id);

        if ($result) {
            $this->_helper->flashMessenger->addMessage('Post supprimé avec succès');
        } else {
            $this->_helper->flashMessenger->addMessage('Erreur lors de la suppression');
        }

        // Redirection vers la liste après suppression
        return $this->_helper->redirector('list');
    }
}
```

## Vues

### list.phtml — Liste des posts
```phtml

<h1>Liste des Posts</h1>

<?php if (!empty($this->messages)): ?>
    <ul class="messages">
        <?php foreach ($this->messages as $msg): ?>
            <li><?= $this->escape($msg) ?></li>
        <?php endforeach; ?>
    </ul>
<?php endif; ?>

<a href="<?= $this->url(['action' => 'add']) ?>">Ajouter un nouveau post</a>

<?php if (!empty($this->posts)): ?>
    <ul>
        <?php foreach ($this->posts as $post): ?>
            <li>
                <strong><?= $this->escape($post['title']) ?></strong><br>
                <a href="<?= $this->url(['action' => 'view', 'id' => $post['id']]) ?>">Voir</a> |
                <a href="<?= $this->url(['action' => 'edit', 'id' => $post['id']]) ?>">Modifier</a> |
                <a href="<?= $this->url(['action' => 'delete', 'id' => $post['id']]) ?>" 
                   onclick="return confirm('Voulez-vous vraiment supprimer ce post ?');">Supprimer</a>
            </li>
        <?php endforeach; ?>
    </ul>
<?php else: ?>
    <p>Aucun post trouvé.</p>
<?php endif; ?>

```
### view.phtml — Affichage d’un post unique
```phtml

<h1><?= $this->escape($this->post['title']) ?></h1>

<p><?= nl2br($this->escape($this->post['body'])) ?></p>

<p><a href="<?= $this->url(['action' => 'list']) ?>">Retour à la liste</a></p>

```
### add.phtml — Formulaire d’ajout d’un post
```phtml
<h1>Ajouter un nouveau post</h1>

<?php if (!empty($this->messages)): ?>
    <ul class="messages">
        <?php foreach ($this->messages as $msg): ?>
            <li><?= $this->escape($msg) ?></li>
        <?php endforeach; ?>
    </ul>
<?php endif; ?>

<form method="post" action="<?= $this->url(['action' => 'add']) ?>">
    <p>
        <label for="title">Titre :</label><br>
        <input type="text" name="title" id="title" value="<?= $this->escape($this->title ?? '') ?>" required>
    </p>
    <p>
        <label for="body">Contenu :</label><br>
        <textarea name="body" id="body" rows="6" required><?= $this->escape($this->body ?? '') ?></textarea>
    </p>
    <p>
        <label for="userId">ID Utilisateur :</label><br>
        <input type="number" name="userId" id="userId" value="<?= $this->escape($this->userId ?? 1) ?>" min="1">
    </p>
    <p>
        <button type="submit">Ajouter</button>
        <a href="<?= $this->url(['action' => 'list']) ?>">Annuler</a>
    </p>
</form>


```
### edit.phtml — Formulaire de modification d’un post
```phtml
<h1>Modifier le post</h1>

<?php if (!empty($this->messages)): ?>
    <ul class="messages">
        <?php foreach ($this->messages as $msg): ?>
            <li><?= $this->escape($msg) ?></li>
        <?php endforeach; ?>
    </ul>
<?php endif; ?>

<form method="post" action="<?= $this->url(['action' => 'edit', 'id' => $this->post['id']]) ?>">
    <p>
        <label for="title">Titre :</label><br>
        <input type="text" name="title" id="title" value="<?= $this->escape($this->post['title']) ?>" required>
    </p>
    <p>
        <label for="body">Contenu :</label><br>
        <textarea name="body" id="body" rows="6" required><?= $this->escape($this->post['body']) ?></textarea>
    </p>
    <p>
        <label for="userId">ID Utilisateur :</label><br>
        <input type="number" name="userId" id="userId" value="<?= $this->escape($this->post['userId']) ?>" min="1">
    </p>
    <p>
        <button type="submit">Modifier</button>
        <a href="<?= $this->url(['action' => 'list']) ?>">Annuler</a>
    </p>
</form>
```

### Ce que fait chaque action :
| Action       | Description                           | Méthode HTTP attendue         |
|--------------|-------------------------------------|------------------------------|
| listAction   | Affiche tous les posts               | GET                          |
| viewAction   | Affiche un post précis               | GET                          |
| addAction    | Formulaire + création d’un post     | GET (form) + POST (submit)   |
| editAction   | Formulaire + modification d’un post | GET (form) + POST (submit)   |
| deleteAction | Supprime un post                    | GET (souvent via lien)        |





