# 1. Introduction à Zend Framework 1

<h1 style="color:#00bcd4;">1. Introduction à Zend Framework 1</h1>

<p>
  <strong>Zend Framework 1</strong> (<span style="color:#ff9800;">ZF1</span>) est un framework PHP 
  <span style="color:#4caf50;">orienté objet</span>, publié pour la première fois en 
  <span style="color:#00bcd4;">2006</span> par Zend Technologies.
</p>

<p>
  Il repose sur l’architecture <strong style="color:#9c27b0;">MVC</strong> 
  (<span style="color:#00bcd4;">Model - View - Controller</span>), 
  ce qui signifie que :
</p>

<ul>
  <li><strong>Model</strong> : Gère la logique métier et l’accès aux données.</li>
  <li><strong>View</strong> : Affiche les données (<span style="color:#009688;">HTML</span>, JSON, XML…).</li>
  <li><strong>Controller</strong> : Fait le lien entre utilisateur, modèle et vue.</li>
</ul>

<h3 style="color:#00bcd4;">Avantages principaux :</h3>
<ul>
  <li><span style="color:#4caf50;">Flexibilité</span> : chaque composant (<code>Zend_Db</code>, <code>Zend_Form</code>…) est réutilisable.</li>
  <li><span style="color:#4caf50;">Multi-format</span> : HTML, JSON, XML… Idéal pour créer des APIs.</li>
  <li><span style="color:#4caf50;">Structure claire</span> : code mieux organisé et maintenable.</li>
  <li><span style="color:#4caf50;">Personnalisation</span> : conventions souples, configuration modulable.</li>
</ul>

<h3 style="color:#00bcd4;">Cycle de vie d’une requête dans ZF1 :</h3>
<ol>
  <li>Requête HTTP → <code>.htaccess</code> → <code>index.php</code></li>
  <li>Initialisation de la configuration (<code>application.ini</code>)</li>
  <li>Analyse de l’URL (Router)</li>
  <li>Appel du Controller & Action</li>
  <li>Récupération des données via le Model</li>
  <li>Transmission des données à la View</li>
  <li>Réponse envoyée au navigateur</li>
</ol>
