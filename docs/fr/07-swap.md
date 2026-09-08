# 7. swap et l'invariant K

`swap(amount0Out, amount1Out, to, data)` procède dans un ordre inhabituel : **il envoie d'abord, il vérifie ensuite**. Le commentaire dit « optimistically transfer tokens ».

Les montants entrants sont ensuite déduits des soldes constatés :

```solidity
uint amount0In = balance0 > _reserve0 - amount0Out
               ? balance0 - (_reserve0 - amount0Out) : 0;
```

Puis vient la seule vérification qui compte :

```solidity
uint balance0Adjusted = balance0.mul(1000).sub(amount0In.mul(3));
uint balance1Adjusted = balance1.mul(1000).sub(amount1In.mul(3));
require(balance0Adjusted.mul(balance1Adjusted)
        >= uint(_reserve0).mul(_reserve1).mul(1000**2), 'UniswapV2: K');
```

Les frais de 0,3 % sont là, et nulle part ailleurs. Retirer `3/1000` du montant entrant avant le test revient à exiger que le produit des réserves augmente d'autant. Le multiplicateur `1000` et le `1000**2` à droite évitent toute division : la comparaison reste en nombres entiers.

Ces frais ne partent pas dans un contrat de collecte. Ils restent dans le pool, ce qui augmente la valeur de chaque part LP. C'est ainsi que les fournisseurs de liquidité sont rémunérés.

Le contrat refuse `to` égal à l'un des deux jetons, et exige au moins une entrée non nulle.

Suite : [les flash swaps](08-flash-swaps.md).
