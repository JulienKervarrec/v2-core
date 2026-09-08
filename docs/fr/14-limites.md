# 14. Limites de ce parcours

Ce parcours est **documentaire**. Rien n'a été installé, compilé ni exécuté pour le rédiger. Tout provient de la lecture des contrats et de leurs commentaires.

La suite de tests existe, dans `test/`, en TypeScript. `UniswapV2Pair.spec.ts` couvre directement les chapitres 5 à 9 ; `UniswapV2Factory.spec.ts` couvre le chapitre 3. C'est le meilleur point de départ pour vérifier ce qui est décrit ici.

Ce dépôt n'est que la moitié du protocole. Le calcul des montants, l'enchaînement de plusieurs paires, les protections contre le glissement et les délais d'expiration vivent dans `v2-periphery`. Employer les fonctions décrites ici directement, sans routeur, expose à perdre des fonds : le code le dit à trois reprises — « this low-level function should be called from a contract which performs important safety checks ».

Deux points ne sont pas traités. La perte impermanente est une conséquence économique de la formule, pas une ligne de code, et mérite un traitement à part. Les jetons non standards — ceux qui prélèvent des frais au transfert, ou dont le solde varie seul — interagissent de façon subtile avec la comptabilité solde/réserve ; les chapitres 6 et 11 en donnent l'idée sans en faire le tour.

Enfin, Uniswap v3 et v4 reposent sur d'autres principes : liquidité concentrée, hooks. Ce parcours ne s'y transpose pas.
