# 13. Le jeton LP et permit

`UniswapV2Pair` hérite de `UniswapV2ERC20` : la part de liquidité n'est pas un jeton séparé, c'est la paire elle-même. Symbole `UNI-V2`, 18 décimales.

L'intérêt du fichier est ailleurs : il implémente `permit`, la signature hors chaîne d'EIP-2612.

Le problème résolu est celui du retrait de liquidité. Sans `permit`, il faut deux transactions : une pour autoriser le routeur à déplacer les parts, une pour retirer. La première coûte du gaz et n'accomplit rien d'utile pour l'utilisateur.

`permit` remplace cette autorisation par une signature. Elle suit EIP-712 :

```solidity
bytes32 digest = keccak256(abi.encodePacked('\x19\x01', DOMAIN_SEPARATOR,
    keccak256(abi.encode(PERMIT_TYPEHASH, owner, spender, value, nonces[owner]++, deadline))));
address recoveredAddress = ecrecover(digest, v, r, s);
require(recoveredAddress != address(0) && recoveredAddress == owner, 'UniswapV2: INVALID_SIGNATURE');
```

Trois protections dans ces quelques lignes. `DOMAIN_SEPARATOR` contient l'identifiant de chaîne et l'adresse du contrat : une signature ne vaut que pour cette paire, sur ce réseau. `nonces[owner]++` interdit le rejeu. `deadline` borne la validité dans le temps.

Le test `recoveredAddress != address(0)` n'est pas redondant : `ecrecover` renvoie l'adresse nulle sur une signature malformée, et l'omettre laisserait passer n'importe quoi si `owner` valait zéro.

Suite : [limites de ce parcours](14-limites.md).
