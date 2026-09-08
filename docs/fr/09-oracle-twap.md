# 9. L'oracle TWAP

Le prix instantané d'une paire est manipulable : un emprunt éclair suffit à le déplacer le temps d'un bloc. Uniswap v2 expose donc autre chose — un prix **cumulé**.

À la première interaction de chaque bloc, `_update` accumule :

```solidity
price0CumulativeLast += uint(UQ112x112.encode(_reserve1).uqdiv(_reserve0)) * timeElapsed;
```

La valeur stockée n'est pas un prix mais une somme de prix pondérée par le temps. Elle ne diminue jamais et n'a aucun sens prise isolément.

Le prix moyen sur un intervalle s'obtient par différence entre deux relevés :

```
prix moyen = (cumul_fin - cumul_début) / (temps_fin - temps_début)
```

Manipuler cette moyenne coûte cher : il faut maintenir le prix faussé pendant toute la fenêtre, en encaissant l'arbitrage à chaque bloc.

Deux détails que le code assume explicitement. Le débordement de `timeElapsed` et celui des accumulateurs sont **voulus** — les commentaires disent « overflow is desired ». L'arithmétique modulaire garantit qu'une différence entre deux relevés reste correcte, même après passage par le maximum. Et l'accumulation n'a lieu qu'une fois par bloc, sur les réserves d'**avant** l'opération : le prix retenu est celui de la fin du bloc précédent, hors d'atteinte d'une manipulation dans le bloc courant.

Suite : [UQ112x112, les nombres à virgule fixe](10-uq112x112.md).
