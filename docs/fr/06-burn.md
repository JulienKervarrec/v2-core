# 6. burn : retirer la liquidité

`burn(to)` suit la même logique inversée, et avec la même surprise : le montant à retirer n'est pas un argument. Le fournisseur envoie d'abord ses parts LP à la paire, puis appelle `burn`. Le contrat lit ce qu'il détient de lui-même :

```solidity
uint liquidity = balanceOf[address(this)];
```

Les montants rendus sont calculés **sur les soldes réels**, pas sur les réserves :

```solidity
amount0 = liquidity.mul(balance0) / _totalSupply;
amount1 = liquidity.mul(balance1) / _totalSupply;
```

Le commentaire justifie ce choix — « using balances ensures pro-rata distribution ». Si quelqu'un a envoyé des jetons à la paire sans appeler `sync`, ce surplus fait partie du solde et se répartit entre tous les porteurs de parts, au lieu de rester coincé.

Après les deux transferts, le contrat relit les soldes avant d'appeler `_update`. Il ne suppose pas que le solde a diminué du montant exact envoyé : un jeton qui prélève des frais au transfert donnerait un résultat différent.

Suite : [swap et l'invariant K](07-swap.md).
