# 3. La factory et l'adresse déterministe

`createPair(tokenA, tokenB)` commence par trier les deux adresses : `token0` est toujours la plus petite des deux, numériquement. Ce tri n'est pas cosmétique — il garantit qu'une paire donnée a une seule représentation possible, donc une seule adresse.

Le déploiement passe par `create2` en assembleur, avec pour sel `keccak256(token0, token1)` :

```solidity
bytes32 salt = keccak256(abi.encodePacked(token0, token1));
assembly { pair := create2(0, add(bytecode, 32), mload(bytecode), salt) }
```

La conséquence est importante : **l'adresse d'une paire se calcule hors chaîne**, à partir des deux adresses de jetons et du code de création. Le routeur s'en sert pour parler à une paire sans jamais interroger la factory, ce qui économise une lecture de stockage à chaque échange.

La table `getPair` est renseignée dans les deux sens, pour que l'ordre des arguments n'ait pas d'importance à la lecture. Le contrôle `getPair[token0][token1] == address(0)` suffit à interdire les doublons, précisément parce que les adresses sont triées.

Enfin, la factory appelle `initialize` sur la paire fraîchement créée. Le constructeur de la paire ne prend aucun argument : c'est ce qui rend son code de création constant, et donc l'adresse prévisible.

Suite : [les réserves et le slot unique](04-reserves.md).
