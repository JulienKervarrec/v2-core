# 12. Les frais de protocole

Les 0,3 % du chapitre 7 vont aux fournisseurs de liquidité. Un second prélèvement, désactivé par défaut, peut détourner **un sixième** de ces frais vers le protocole. Il est piloté par `feeTo` dans la factory : à zéro, rien n'est prélevé.

`_mintFee` est appelée au début de `mint` et de `burn`, jamais pendant un échange. Le protocole n'est donc pas payé en continu : sa part est calculée d'un coup, aux seuls moments où la liquidité bouge.

La mesure passe par la croissance de `√k` :

```solidity
uint rootK = Math.sqrt(uint(_reserve0).mul(_reserve1));
uint rootKLast = Math.sqrt(_kLast);
uint numerator = totalSupply.mul(rootK.sub(rootKLast));
uint denominator = rootK.mul(5).add(rootKLast);
uint liquidity = numerator / denominator;
```

Pourquoi la racine carrée ? Parce que `k` augmente aussi quand quelqu'un ajoute de la liquidité. `√k` rapporté au nombre de parts, en revanche, ne croît que sous l'effet des frais accumulés. Le `5` du dénominateur produit la fraction d'un sixième.

Le paiement se fait en parts LP nouvellement émises, envoyées à `feeTo` — le protocole devient fournisseur de liquidité, il ne retire aucun jeton.

Quand les frais sont éteints, `kLast` est remis à zéro pour ne pas facturer rétroactivement une période non couverte à la réactivation.

Suite : [le jeton LP et permit](13-jeton-lp-permit.md).
