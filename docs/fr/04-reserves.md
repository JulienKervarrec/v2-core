# 4. Les réserves et le slot unique

Trois variables se partagent un seul emplacement de stockage :

```solidity
uint112 private reserve0;
uint112 private reserve1;
uint32  private blockTimestampLast;
```

112 + 112 + 32 = 256 bits exactement. Lire les trois coûte une seule opération `SLOAD`, les écrire un seul `SSTORE`. C'est la raison du plafond de `uint112` sur les réserves, et `_update` le fait respecter avec `require(balance0 <= uint112(-1) ...)`.

La distinction entre **réserve** et **solde** est le point à retenir. La réserve est ce que le contrat croit détenir ; le solde est ce qu'il détient vraiment, lu par `balanceOf`. Toutes les fonctions comparent les deux : la différence est ce que l'appelant vient d'apporter.

Ce choix explique la forme des fonctions. On ne passe pas de montant en argument : on transfère d'abord les jetons à la paire, puis on appelle `mint` ou `swap`, qui déduisent le montant de l'écart. D'où l'avertissement du code — ces fonctions doivent être appelées depuis un contrat, et les deux opérations doivent tenir dans une seule transaction.

Le modificateur `lock` interdit la réentrance sur toutes les fonctions publiques.

Suite : [mint, ajouter de la liquidité](05-mint.md).
