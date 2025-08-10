# 3. Le fichier public/.htaccess

Ce fichier est essentiel pour une réécriture propre des URLs :
- Il redirige toutes les requêtes vers index.php sauf si un fichier/dossier existe (images, CSS, JS…).
- Permet d’avoir des URLs conviviales, sans le index.php visible.

Ce fichier configure des règles de réécriture d’URL avec mod_rewrite d’Apache, souvent utilisé pour des frameworks ou applications web pour rediriger toutes les requêtes vers un point d’entrée unique (index.php).

```plaintext
RewriteEngine On
RewriteBase /

RewriteCond %{REQUEST_FILENAME} -s [OR]
RewriteCond %{REQUEST_FILENAME} -l [OR]
RewriteCond %{REQUEST_FILENAME} -d
RewriteRule ^.*$ - [NC,L]

RewriteRule ^.*$ index.php [NC,L] 
```

Explications de cet exemple de ce rewrite : 

## RewriteEngine On : ##

- Active le moteur de réécriture d’URL (mod_rewrite).
- Sans cette directive, les règles suivantes ne fonctionneraient pas.

## RewriteBase / : ##

- Définit la base à partir de laquelle s’appliquent les règles de réécriture.
- Ici, la base est la racine du site, donc toutes les règles sont relatives à `/`.

## RewriteCond %{REQUEST_FILENAME} -s [OR] : ##

- Vérifie si la requête correspond à un fichier existant et non vide.
- Le flag `[OR]` indique que cette condition est reliée par un "OU" à la suivante.

## RewriteCond %{REQUEST_FILENAME} -l [OR] : ##

- Vérifie si la requête correspond à un lien symbolique (symlink).
- Toujours relié par un "OU" à la prochaine condition.

## RewriteCond %{REQUEST_FILENAME} -d : ##

- Vérifie si la requête correspond à un dossier existant.
- Pas de `[OR]` ici, c’est la dernière condition du groupe.

## RewriteRule ^.*$ - [NC,L] : ##

- Si l’une des conditions précédentes est vraie (fichier, lien, dossier existant),
- alors ne pas réécrire l’URL (le tiret `-` signifie “pas de changement”).
- Flags :
  - `[NC]` : insensible à la casse (case-insensitive).
  - `[L]` : dernière règle à appliquer si elle correspond.

## RewriteRule ^.*$ index.php [NC,L] : ##

- Pour toutes les autres requêtes (non correspondantes à un fichier, lien ou dossier),
- redirige vers `index.php`, qui devient le point d’entrée unique de l’application.
- Flags :
  - `[NC]` : insensible à la casse.
  - `[L]` : dernière règle.


