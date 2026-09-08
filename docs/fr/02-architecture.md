# 2. Architecture du dépôt

Trois contrats et trois bibliothèques, tous dans `contracts/`.

`UniswapV2Factory.sol` (49 lignes) est le registre. Il crée les paires, tient la table `getPair` et détient les deux réglages du protocole : `feeTo` et `feeToSetter`.

`UniswapV2Pair.sol` (201 lignes) est le contrat qui fait tout le travail : réserves, ajout et retrait de liquidité, échanges, oracle. C'est le seul fichier vraiment dense du dépôt.

`UniswapV2ERC20.sol` (94 lignes) est le jeton de part de liquidité. `UniswapV2Pair` en hérite : une paire **est** son propre jeton LP.

Les bibliothèques sont minuscules et chacune règle un problème précis. `SafeMath` protège des débordements — Solidity 0.5 ne le fait pas tout seul. `Math` fournit `min` et une racine carrée par la méthode de Babylone. `UQ112x112` implémente les nombres à virgule fixe utilisés par l'oracle.

`contracts/interfaces/` déclare les interfaces, dont `IUniswapV2Callee` qui rend possibles les flash swaps.

Suite : [la factory et l'adresse déterministe](03-factory.md).
