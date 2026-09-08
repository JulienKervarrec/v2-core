# 8. Les flash swaps

Une seule ligne de `swap` ouvre une possibilité entière :

```solidity
if (data.length > 0) IUniswapV2Callee(to).uniswapV2Call(msg.sender, amount0Out, amount1Out, data);
```

Si l'appelant fournit un `data` non vide, la paire rappelle le destinataire **après** lui avoir envoyé les jetons, mais **avant** de vérifier l'invariant K. Pendant cet appel, le destinataire détient des jetons qu'il n'a pas encore payés.

Il peut en faire ce qu'il veut, à une condition : au retour, les soldes de la paire doivent satisfaire le test K. Trois façons de s'en acquitter, et le code ne les distingue pas :

- rendre le même jeton, avec les frais de 0,3 % — c'est un prêt éclair ;
- payer avec l'autre jeton — c'est un échange ordinaire, réglé après coup ;
- un mélange des deux.

Aucune limite de montant autre que la réserve, aucun collatéral, aucune liste d'autorisation. La sécurité tient entièrement à l'atomicité : si le test K échoue, toute la transaction est annulée, y compris le transfert initial.

C'est ce mécanisme qui alimente l'arbitrage sans capital et les remboursements de position en une transaction.

Suite : [l'oracle TWAP](09-oracle-twap.md).
