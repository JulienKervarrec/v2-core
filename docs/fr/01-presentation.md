# 1. Présentation

Uniswap v2 est un teneur de marché automatisé : il n'y a pas de carnet d'ordres, pas de contrepartie humaine. Chaque paire de jetons vit dans un contrat qui détient une réserve de chacun des deux, et le prix découle mécaniquement du rapport entre ces réserves.

La règle tient en une formule. Le produit des deux réserves doit rester au moins constant après chaque échange : `x · y ≥ k`. Qui retire du jeton A doit apporter assez de jeton B pour que le produit ne diminue pas. Plus on retire, plus le prix se dégrade — c'est le glissement.

Ce dépôt contient le **cœur** du protocole : trois contrats, environ 350 lignes. Il ne contient pas le routeur, qui calcule les montants, enchaîne les paires et protège l'utilisateur ; celui-ci vit dans `v2-periphery`. Les fonctions de ce dépôt sont volontairement brutes et le code le dit lui-même : elles doivent être appelées depuis un contrat qui effectue les vérifications de sécurité.

Suite : [architecture du dépôt](02-architecture.md).
