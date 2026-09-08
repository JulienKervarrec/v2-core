# 11. skim et sync

Rien n'empêche d'envoyer des jetons directement à une paire, ni un jeton exotique de modifier le solde du contrat tout seul. Le solde et la réserve peuvent donc diverger. Deux fonctions rattrapent l'écart, dans les deux sens opposés.

`skim(to)` **force le solde à rejoindre la réserve** : il envoie l'excédent à l'adresse indiquée.

```solidity
_safeTransfer(_token0, to, IERC20(_token0).balanceOf(address(this)).sub(reserve0));
```

C'est le mécanisme de récupération : n'importe qui peut appeler `skim` et empocher le surplus. Un utilisateur qui envoie des jetons à une paire par erreur les perd au profit du premier venu.

`sync()` **force la réserve à rejoindre le solde** : il appelle `_update` avec les soldes réels, intégrant le surplus au pool.

```solidity
_update(IERC20(token0).balanceOf(address(this)), IERC20(token1).balanceOf(address(this)), reserve0, reserve1);
```

Le cas que ces fonctions rendent survivable est celui d'un jeton dont le solde gonfle sans transfert — un jeton à intérêt intégré, par exemple. Sans `sync`, la réserve resterait figée sous le plafond `uint112` et la paire finirait bloquée sur le `require(... <= uint112(-1))` de `_update`.

Suite : [les frais de protocole](12-frais-protocole.md).
