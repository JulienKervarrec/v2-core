# 5. mint : ajouter de la liquidité

`mint(to)` mesure l'apport comme la différence entre solde et réserve, puis émet des parts.

Le premier fournisseur est traité à part : il n'y a pas de rapport existant à respecter, il fixe donc le prix initial. Sa part vaut la moyenne géométrique des deux apports, moins une constante :

```solidity
liquidity = Math.sqrt(amount0.mul(amount1)).sub(MINIMUM_LIQUIDITY);
_mint(address(0), MINIMUM_LIQUIDITY);
```

Ces 1000 parts sont envoyées à l'adresse zéro, définitivement perdues. Le commentaire du code dit « permanently lock ». Elles garantissent que `totalSupply` ne revient jamais à zéro, ce qui rendrait la valeur d'une part manipulable par un attaquant qui viderait puis regonflerait le pool.

Pour les suivants, la part est proportionnelle, et c'est le **minimum** des deux ratios qui l'emporte :

```solidity
liquidity = Math.min(amount0.mul(_totalSupply) / _reserve0,
                     amount1.mul(_totalSupply) / _reserve1);
```

Apporter un jeton en excès par rapport au rapport courant ne rapporte donc rien de plus : le surplus est simplement offert au pool. C'est au routeur de calculer les deux montants équilibrés.

Suite : [burn, retirer la liquidité](06-burn.md).
