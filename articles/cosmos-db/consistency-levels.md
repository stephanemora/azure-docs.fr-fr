---
title: Niveaux de cohérence dans Azure Cosmos DB
description: Azure Cosmos DB offre cinq niveaux de cohérence qui permettent de faire des compromis avisés entre cohérence éventuelle, disponibilité et latence.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 4e3d29471064616039bf946bb2762c15ce67bf8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530255"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Niveaux de cohérence dans Azure Cosmos DB

Les bases de données distribuées qui reposent sur la réplication afin d’offrir une haute disponibilité, une faible latence ou les deux, constituent le compromis fondamental entre la cohérence de la lecture et la disponibilité, la latence et le débit. La plupart des bases de données distribuées qui sont commercialisées demandent aux développeurs de choisir entre les deux modèles de cohérence extrêmes : une cohérence *forte* et une cohérence *éventuelle*. La linéarisabilité ou le modèle de cohérence fort constitue la référence en matière de programmabilité des données. Mais il est proposé au prix d’une latence élevée (dans un état stable) et d’une perte de disponibilité (en cas de défaillance). Dans le même temps, la cohérence éventuelle offre une plus haute disponibilité et de meilleures performances, mais la programmation des applications est devient difficile. 

Azure Cosmos DB aborde la cohérence des données sous forme d’un éventail de choix, plutôt que de proposer deux extrêmes. La cohérence forte et la cohérence éventuelle constituent les deux extrémités du spectre, mais il existe de nombreuses possibilités entre les deux. Les développeurs peuvent utiliser ces options pour faire des choix précis et des compromis granulaires en termes de haute disponibilité et de performance. 

Avec Azure Cosmos DB, les développeurs peuvent faire leur choix parmi cinq modèles de cohérence bien définis sur le spectre de la cohérence. De la cohérence la plus forte à la plus souple, les modèles incluent *forte*, *obsolescence limitée*, *session*, *préfixe cohérent* et *éventuelle*. Les modèles sont bien définis et intuitifs et ils peuvent être utilisés pour des scénarios réels spécifiques. Chaque modèle propose des [compromis clairs entre disponibilité et performances](consistency-levels-tradeoffs.md), et ces modèles sont appuyés par des contrats de niveau de service. L’illustration suivante montre les différents niveaux de cohérence en tant que spectre.

![La cohérence en tant que spectre](./media/consistency-levels/five-consistency-levels.png)

Les niveaux de cohérence sont indépendants des régions et garantis pour toutes les opérations, indépendamment de la région d’émission des lectures et des écritures, du nombre de régions associées à votre compte Azure Cosmos ou de la configuration de votre compte avec une ou plusieurs régions d’écriture.

## <a name="scope-of-the-read-consistency"></a>Étendue de la cohérence de lecture

La cohérence de lecture s’applique à une seule opération de lecture dans la plage de clé de partition ou partition logique. L’opération de lecture peut être émise par un client distant ou une procédure stockée.

## <a name="configure-the-default-consistency-level"></a>Configurer le niveau de cohérence par défaut

Vous pouvez configurer le niveau de cohérence par défaut sur votre compte Azure Cosmos DB à tout moment. Le niveau de cohérence par défaut configuré sur votre compte s’applique à toutes les bases de données Azure Cosmos et tous les conteneurs sous ce compte. Toutes les lectures et requêtes émises vers un conteneur ou une base de données utilisent le niveau de cohérence par défaut spécifié. Pour en savoir plus, consultez [Configurer le niveau de cohérence par défaut](how-to-manage-consistency.md#configure-the-default-consistency-level).

## <a name="guarantees-associated-with-consistency-levels"></a>Garanties associées aux niveaux de cohérence

Les contrats de niveau de service complets fournis par Azure Cosmos DB garantissent que 100 % des requêtes de lecture respecteront la garantie de cohérence dans le cadre du niveau de cohérence choisi. Une requête de lecture respecte le contrat de niveau de service de cohérence si toutes les garanties de cohérence associées au niveau de cohérence sont satisfaites. Les définitions précises des cinq niveaux de cohérence dans Azure Cosmos DB (en utilisant le langage de spécification TLA +) sont fournies dans le référentiel GitHub [azure-cosmos-tla](https://github.com/Azure/azure-cosmos-tla).

La sémantique des cinq niveaux de cohérence est décrite ici :

- **Remarque**: une cohérence forte offre une garantie de linéarisabilité. La linéarisabilité fait référence aux demandes de traitement simultanées. Garantit que les lectures retournent la version validée la plus récente d’un élément. Un client ne voit jamais une écriture partielle ou non validée. Les utilisateurs sont toujours assurés de lire la toute dernière écriture validée.

  Le graphique suivant illustre la cohérence forte avec des notes musicales. Une fois les données écrites dans la région « USA Ouest 2 », quand vous lisez les données à partir d’autres régions, vous obtenez la valeur la plus récente :

  ![video](media/consistency-levels/strong-consistency.gif)

- **Obsolescence limitée**: les lectures honoreront la garantie de préfixe cohérent. Les lectures sont retardées derrière les écritures par, au plus, des versions *« K »* (c’est-à-dire des « mises à jour ») d’un élément ou un intervalle de temps *« T »* . En d’autres termes, lorsque vous choisissez l’obsolescence limitée, « l’obsolescence » peut être configurée de deux manières : 

  * Nombre de versions (*K*) de l’élément
  * Intervalle de temps (*T*) pendant lequel les lectures peuvent être en retard par rapport aux écritures 

  La cohérence de type obsolescence limitée fournit l’ordre global total, en dehors de la « fenêtre d’obsolescence ». Notez que des garanties de lecture unitone existent dans une région à l’intérieur et en dehors de la fenêtre d’obsolescence. La cohérence forte dispose de la même sémantique que l’obsolescence limitée. La fenêtre d’obsolescence est égale à zéro. L’obsolescence limitée est également appelée linéarisabilité retardée. Lorsqu’un client effectue des opérations de lecture dans une région acceptant les écritures, les garanties fournies par une cohérence à obsolescence limitée sont identiques à celles à forte cohérence.

  L’obsolescence limitée est souvent choisie par des applications mondialement distribuées qui souhaitent de faibles latences en écriture, mais nécessitent des garanties d’ordre totales à l’échelle mondiale. L’obsolescence limitée est idéale pour les applications qui incluent la collaboration et le partage de groupes, les cotations boursières, la publication-abonnement/la mise en file d’attente, etc. Le graphique suivant illustre la cohérence à obsolescence limitée avec des notes musicales. Une fois les données écrites dans la région « USA Ouest 2 », les régions « USA Est 2 » et « Australie Est » lisent la valeur écrite en fonction de la durée de latence maximum configurée ou du nombre maximal d’opérations :

  ![video](media/consistency-levels/bounded-staleness-consistency.gif)

- **Session**:  Dans une session client unique, les lectures honoreront les garanties de préfixe cohérent (en partant du principe qu’il s’agit d’une session à « writer » unique), de lectures unitones, d’écritures unitones, de lecture de vos écritures et d’écriture suivant les lectures. Les clients en dehors de la session effectuant des écritures verront la cohérence éventuelle.

  La cohérence de session est le niveau de cohérence largement utilisé aussi bien pour les applications limitées à une seule région que celles distribuées dans le monde entier. Elle propose des latences en écriture, une disponibilité et un débit de lecture comparables à ceux de la cohérence éventuelle, mais fournit aussi des garanties de cohérence qui répondent aux besoins des applications écrites pour agir dans le contexte de l’utilisateur. Le graphique suivant illustre la cohérence de session avec des notes musicales. Les régions « USA Ouest 2 » et « USA Est 2 » utilisent la même session (session A) et lisent donc les données en même temps. En revanche, la région « Australie Est » utilise « Session B » et reçoit les données plus tard, mais dans le même ordre que les écritures.

  ![video](media/consistency-levels/session-consistency.gif)

- **Préfixe cohérent** : les mises à jour retournées contiennent un préfixe de toutes les mises à jour, sans interruption. Le niveau de cohérence avec préfixe cohérent garantit que les lectures ne verront jamais d’écritures désordonnées.

  Si les écritures ont été effectuées dans l’ordre `A, B, C`, un client voit `A`, `A,B` ou `A,B,C`, mais jamais dans le désordre comme `A,C` ou `B,A,C`. La cohérence avec préfixe cohérent fournit des latences d’écriture, une disponibilité et un débit de lecture comparables à ceux de la cohérence éventuelle, mais offre également des garanties d’ordre adaptées aux besoins des scénarios dans lesquels l’ordre est important. Le graphique suivant illustre la cohérence avec préfixe cohérent avec des notes musicales. Dans toutes les régions, les lectures ne voient jamais d’écritures dans le désordre :

  ![video](media/consistency-levels/consistent-prefix.gif)

- **Eventual (Éventuel)** : Il n’existe aucune garantie de classement pour les lectures. En l’absence d’autres écritures, les réplicas finissent par converger.  
La cohérence éventuelle est la forme de cohérence la plus faible, car un client peut lire des valeurs plus anciennes que celles qu’il a déjà lues. La cohérence éventuelle est idéale lorsque l’application ne nécessite pas de garantie d’ordre. Comme exemples, citons le nombre de retweets, de mentions J’aime ou de commentaires non liés à un thread. Le graphique suivant illustre la cohérence éventuelle avec des notes musicales.

  ![video](media/consistency-levels/eventual-consistency.gif)

## <a name="additional-reading"></a>Documentation supplémentaire

Pour en savoir plus sur les concepts de cohérence, lisez les articles suivants :

- [Spécifications TLA + de haut niveau pour les cinq niveaux de cohérence proposés par Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla)
- [La cohérence des données répliquées expliquée par le base-ball (vidéo) par Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
- [La cohérence des données répliquées expliquée par le base-ball (livre blanc) par Doug Terry](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F157411%2Fconsistencyandbaseballreport.pdf)
- [Garanties de session pour des données répliquées peu cohérentes](https://dl.acm.org/citation.cfm?id=383631)
- [Compromis de cohérence en termes de conception de systèmes de base de données distribuée modernes : CAP n’est qu’une partie de l’histoire](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf) (Probabilités en fonction de l’obsolescence limitée (PBS) pour les quorums partiels pratiques)
- [Niveau de cohérence éventuel repensé](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les niveaux de cohérence dans Azure Cosmos DB, lisez les articles suivants :

* [Choisir le bon niveau de cohérence pour votre application](consistency-levels-choosing.md)
* [Niveaux de cohérence pour les API Azure Cosmos DB](consistency-levels-across-apis.md)
* [Compromis entre disponibilité et performance pour différents niveaux de cohérence](consistency-levels-tradeoffs.md)
* [Configurer le niveau de cohérence par défaut](how-to-manage-consistency.md#configure-the-default-consistency-level)
* [Remplacer le niveau de cohérence par défaut](how-to-manage-consistency.md#override-the-default-consistency-level)

