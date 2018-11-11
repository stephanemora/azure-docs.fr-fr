---
title: Niveaux de cohérence dans Azure Cosmos DB | Microsoft Docs
description: Azure Cosmos DB offre cinq niveaux de cohérence qui permettent de faire des compromis avisés entre cohérence éventuelle, disponibilité et latence.
keywords: cohérence éventuelle, azure cosmos db, azure, Microsoft Azure
services: cosmos-db
author: aliuy
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: andrl
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5cb439f7fe6461fcef0d010535179e16e28c294a
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50239167"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Niveaux de cohérence dans Azure Cosmos DB

Les bases de données distribuées qui reposent sur la réplication afin d’offrir une haute disponibilité, une faible latence ou les deux, constituent le compromis fondamental entre la cohérence de la lecture et la disponibilité, la latence et le débit. La plupart des bases de données distribuées commercialisées demandent aux développeurs de choisir entre les deux modèles de cohérence extrêmes : une cohérence forte et une cohérence éventuelle. Même si la  [linéarisabilité](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) ou le modèle de cohérence forte constitue la référence en matière de programmabilité des données, celui-ci entraîne une latence élevée (dans un état stable) et une perte de disponibilité (lors des défaillances). Dans le même temps, la cohérence éventuelle offre une plus haute disponibilité et de meilleures performances, mais elle offre peu de flexibilité de programmation.

Cosmos DB aborde la cohérence des données sous forme d’un éventail de choix, plutôt que de ne proposer que deux extrêmes. La cohérence forte et la cohérence éventuelle constituent les deux extrémités du spectre, mais il existe de nombreuses possibilités de cohérence entre les deux. Ces options de cohérence permettent aux développeurs d’effectuer des choix beaucoup plus précis quant aux compromis à faire entre haute disponibilité et performances. Cosmos DB permet aux développeurs de choisir entre cinq modèles de cohérence bien définis couvrant tout l’éventail de cohérences (du plus fort au plus faible) : **fort**, **obsolescence limitée**, **session**, **préfixe cohérent** et **éventuel**. Chacun de ces modèles de cohérence est bien défini, intuitif, et peut être utilisé pour des scénarios réels spécifiques. Chacun des cinq modèles de cohérence propose des compromis clairs entre disponibilité et performances, et sont appuyés par des contrats de niveau de service complets.

![Spectre de cohérence](./media/consistency-levels/five-consistency-levels.png)
**Spectre de cohérence**

Remarquez que les niveaux de cohérence ne dépendent pas des régions. Le niveau de cohérence (et les garanties de cohérence correspondantes) de votre compte Cosmos DB, est garanti pour toutes les opérations de lecture, sans que les éléments suivants n’entrent en compte :

- Région d’émission des lectures et des écritures
- Nombre de régions associées à votre compte Cosmos
- Que votre compte soit configuré avec une ou plusieurs régions d’écriture

## <a name="scope-of-the-read-consistency"></a>Étendue de la cohérence de lecture

La cohérence de lecture s’applique à une seule opération de lecture dans la plage de clé de partition (également appelée une partition logique). L’opération de lecture peut être émise par un client distant ou une procédure stockée.

## <a name="configuring-the-default-consistency-level"></a>Configuration du niveau de cohérence par défaut

Vous pouvez configurer le **niveau de cohérence par défaut** sur votre compte Cosmos DB à tout moment. Le niveau de cohérence par défaut configuré sur votre compte s’applique à toutes les bases de données Cosmos (et les conteneurs) sous ce compte. Toutes les lectures et requêtes émises vers un conteneur ou une base de données utilisent ce niveau de cohérence par défaut. Consultez cet article pour savoir comment [configurer le niveau de cohérence par défaut](how-to-manage-consistency.md#configure-the-default-consistency-level).

## <a name="guarantees-associated-with-consistency-levels"></a>Garanties associées aux niveaux de cohérence

Les contrats de niveau de service complets fournis par Azure Cosmos DB garantissent que 100 % des requêtes de lecture respecteront la garantie de cohérence dans le cadre du niveau de cohérence que vous choisissez. Une demande de lecture est considérée comme respectant le contrat de niveau de service de cohérence si toutes les garanties de cohérence associées au niveau de cohérence sont satisfaites. Les définitions précises des cinq niveaux de cohérence dans Cosmos DB (en utilisant le [langage de spécification TLA +](http://lamport.azurewebsites.net/tla/tla.html)) sont fournies dans le référentiel GitHub [azure-cosmos-tla](https://github.com/Azure/azure-cosmos-tla). La sémantique des cinq niveaux de cohérence est décrite ci-dessous :

- [Niveau de cohérence = « fort »](https://aphyr.com/posts/313-strong-consistency-models) : une cohérence forte offre une garantie de **linéarisabilité** qui permet de s’assurer que les lectures renvoient la version validée la plus récente d’un élément. Un client ne voit jamais d’écriture partielle ou non validée, et il est assuré de lire l’écriture validée la plus récente.
- **Niveau de cohérence = « obsolescence limitée »** : les lectures honoreront la garantie de préfixe cohérent. Les lectures sont retardées derrière les écritures par, au plus, des versions ou préfixes K (c’est-à-dire des mises à jour) d’un élément ou un intervalle de temps t. Par conséquent, lors du choix du niveau de cohérence obsolescence limitée, l’« obsolescence » peut être configurée de deux façons : par le nombre de versions (K) de l’élément ou de l’intervalle de temps (t) par lequel les lectures sont retardées derrière les écritures. La cohérence de type obsolescence limitée fournit l’ordre global total, en dehors de la « fenêtre d’obsolescence ». Notez que des garanties de lecture unitone existent dans une région à l’intérieur et en dehors de la « fenêtre d’obsolescence ». La cohérence forte dispose de la même sémantique que l’obsolescence limitée, mais avec la « fenêtre d’obsolescence » égale à zéro. L’obsolescence limitée est également appelée « linéarisabilité retardée ». Lorsqu’un client effectue des opérations de lecture dans une région acceptant les écritures, les garanties fournies par une cohérence à obsolescence limitée sont identiques à celles à forte cohérence.
- **Niveau de cohérence = « session »** : les lectures honoreront les garanties de préfixe cohérent, de lectures unitones, d’écritures unitones, de lecture de vos écritures et d’écriture suivant les lectures. La cohérence de session est limitée à une session client.
- **Niveau de cohérence = « préfixe cohérent »**  : les mises à jour retournées sont un préfixe de toutes les mises à jour, sans interruption. Le niveau de cohérence préfixe cohérent garantit que les lectures ne voient jamais d’écritures dans le désordre.
- **Niveau de cohérence = « éventuel »** : il n’existe aucune garantie de classement pour les lectures. En l’absence d’autres écritures, les réplicas finissent par converger.

## <a name="consistency-levels-explained-through-baseball"></a>Niveaux de cohérence – Exemple du baseball

Comme dans l’exemple du document [Replicated Data Consistency Explained Through Baseball](https://www.microsoft.com/en-us/research/wp-content/uploads/2011/10/ConsistencyAndBaseballReport.pdf) (Cohérence des données répliquées – Exemple du baseball), imaginez une séquence d’écritures qui représente le score d’un match de baseball par manche. Dans ce match de baseball hypothétique, nous sommes au milieu de la septième manche (soit à la pause de la septième manche), et l’équipe hôte gagne 5 à 2.

| | **1** | **2** | **3** | **4** | **5** | **6** | **7** | **8** | **9** | **Points** |
| - | - | - | - | - | - | - | - | - | - | - |
| **Visiteurs** | 0 | 0 | 1 | 0 | 5. | 0 | 0 |  |  | 2 |
| **Locaux** | 1 | 0 | 1 | 1 | 0 | 2 |  |  |  | 5. |

Un conteneur Cosmos DB conserve les totaux des points des visiteurs et de l’équipe locale. Lorsque le match est en cours, il se peut que les clients lisent des scores différents à cause des différentes garanties de lecture. Le tableau suivant répertorie l’ensemble complet des scores qui pourraient être renvoyés par la lecture des scores des visiteurs et des locaux, en fonction des cinq garanties de cohérence. Notez que le score des visiteurs est listé en premier, et les différentes valeurs renvoyées possibles sont séparées par des virgules.

| **Niveau de cohérence** | **Scores** |
| - | - |
| **Fort** | 2-5 |
| **Obsolescence limitée** | scores obsolètes (d’une manche au tout au plus) : 2-3, 2-4, 2-5 |
| **Session** | <ul><li>pour l’auteur : 2-5</li><li> pour toute personne autre que l’auteur : 0-0, 0-1, 0-2, 0-3, 0-4, 0-5, 1-0, 1-1, 1-2, 1-3, 1-4, 1-5, 2-0, 2-1, 2-2, 2-3, 2-4, 2-5</li><li>après lecture de 1-3 : 1-3, 1-4, 1-5, 2-3, 2-4, 2-5</li> |
| **Préfixe cohérent** | 0-0, 0-1, 1-1, 1-2, 1-3, 2-3, 2-4, 2-5 |
| **Éventuel** | 0-0, 0-1, 0-2, 0-3, 0-4, 0-5, 1-0, 1-1, 1-2, 1-3, 1-4, 1-5, 2-0, 2-1, 2-2, 2-3, 2-4, 2-5 |

## <a name="additional-reading"></a>Documentation supplémentaire

Pour en savoir plus sur les concepts de cohérence, lisez les articles suivants :

- [Spécifications TLA + de haut niveau pour les cinq niveaux de cohérence proposés par Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla)
- [La cohérence des données répliquées expliquée par le baseball (vidéo) par Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
- [La cohérence des données répliquées expliquée par le baseball (livre blanc) par Doug Terry](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F157411%2Fconsistencyandbaseballreport.pdf)
- [Garanties de session pour des données répliquées peu cohérentes](https://dl.acm.org/citation.cfm?id=383631)
- [Compromis de cohérence en termes de conception de systèmes de base de données distribuée modernes : CAP n’est qu’une partie de l’histoire](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html)
- [Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf) (Probabilités en fonction de l’obsolescence limitée (PBS) pour les quorums partiels pratiques)
- [Niveau de cohérence éventuel repensé](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les niveaux de cohérence dans Cosmos DB, lisez les articles suivants :

* [Choisir le bon niveau de cohérence pour votre application](consistency-levels-choosing.md)
* [Niveaux de cohérence pour les API Cosmos DB](consistency-levels-across-apis.md)
* [Compromis entre disponibilité et performance pour différents niveaux de cohérence](consistency-levels-tradeoffs.md)
* [Comment configurer le niveau de cohérence par défaut](how-to-manage-consistency.md#configure-the-default-consistency-level)
* [Comment remplacer le niveau de cohérence par défaut](how-to-manage-consistency.md#override-the-default-consistency-level)

