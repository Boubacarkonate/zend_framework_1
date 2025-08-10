# 2. Structure type d’un projet ZF1

```plaintext
mon_projet/
│
├── application/      # Code métier, config, MVC
│   ├── configs/
│   ├── controllers/
│   ├── models/
│   ├── views/
│   └── Bootstrap.php
│
├── library/          # Bibliothèques (Zend, perso)
│
├── public/           # Point d’entrée web
│   ├── index.php
│   └── .htaccess
│
└── data/             # Logs, cache...
```

- Le dossier public/ est seul exposé au web, les autres dossiers sont protégés.

- library/ contient Zend Framework et autres librairies.

- application/ contient la logique et la configuration.