---
title: Choisir le niveau de cohérence approprié dans votre application Azure Cosmos DB
description: Choisir le bon niveau de cohérence pour votre application dans Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: cea157e272a2bf464141e592b8e742f314a83233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441919"
---
# <a name="choose-the-right-consistency-level"></a>Choisir le bon niveau de cohérence 

Les bases de données distribuées qui reposent sur la réplication afin d’offrir une haute disponibilité, une faible latence ou les deux, constituent le compromis fondamental entre la cohérence de la lecture et la disponibilité, la latence et le débit. La plupart des bases de données distribuées qui sont commercialisées demandent aux développeurs de choisir entre les deux modèles de cohérence extrêmes : une cohérence *forte* et une cohérence *éventuelle*. Azure Cosmos DB permet aux développeurs de choisir parmi les cinq modèles de cohérence bien définis : *fort*, *obsolescence limitée*, *session*, *préfixe cohérent* et *éventuel*. Chacun de ces modèles de cohérence est bien défini, intuitif, et peut être utilisé pour des scénarios réels spécifiques. Chacun des cinq modèles de cohérence propose des [compromis clairs et précis entre disponibilité et performances](consistency-levels-tradeoffs.md) et est appuyé par des contrats de niveau de service complets. Les simples considérations suivantes vous aideront à faire le bon choix dans de nombreux scénarios courants.

## <a name="sql-api-and-table-api"></a>API SQL et API Table

Tenez compte des points suivants si votre application est construite à l’aide de l’API SQL ou de l’API Table :

- Pour de nombreux scénarios réels, la cohérence de session est optimale et constitue l’option recommandée. Pour plus d’informations, consultez [Gestion du jeton de session pour votre application](how-to-manage-consistency.md#utilize-session-tokens).

- Si votre application nécessite une cohérence forte, il est recommandé d’utiliser le niveau de cohérence Obsolescence limitée.

- Si vous avez besoin de garanties de cohérence plus strictes que celles fournies par la cohérence de session et d’une latence en millisecondes à un chiffre pour les écritures, il est recommandé d’utiliser un niveau de cohérence en fonction de l’obsolescence.  

- Si votre application nécessite une cohérence éventuelle, il est recommandé d’utiliser le niveau de cohérence à préfixe cohérent.

- Si vous avez besoin de garanties de cohérence moins strictes que celles fournies par la cohérence de session, il est recommandé d’utiliser le niveau de cohérence à préfixe cohérent.

- Si vous avez besoin de la disponibilité la plus élevée et de la latence la plus faible, utilisez le niveau de cohérence éventuelle.

- Si vous avez besoin d'une plus grande durabilité des données sans sacrifier les performances, vous pouvez créer un niveau de cohérence personnalisé au niveau de la couche Application. Pour plus d'informations, consultez [Guide pratique sur l'implémentation de la synchronisation personnalisée dans vos applications](how-to-custom-synchronization.md).

## <a name="cassandra-mongodb-and-gremlin-apis"></a>API Cassandra, MongoDB et Gremlin

- Pour plus d’informations sur le mappage entre le « niveau de cohérence de lecture » et les niveaux de cohérence offerts par Apache Cassandra et Cosmos DB, consultez [Niveaux de cohérence et API Cosmos DB](consistency-levels-across-apis.md#cassandra-mapping).

- Pour plus d’informations sur le mappage entre le « problème de lecture » de MongoDB et les niveaux de cohérence Azure Cosmos DB, consultez [Niveaux de cohérence et API Cosmos DB](consistency-levels-across-apis.md#mongo-mapping).

## <a name="consistency-guarantees-in-practice"></a>Garanties de cohérence en pratique

En pratique, vous pouvez obtenir de meilleures garanties de cohérence. Les garanties de cohérence pour une opération de lecture correspondent à l’actualisation et au classement de l’état de la base de données que vous demandez. La cohérence de lecture est liée au classement et à la propagation des opérations d’écriture/mise à jour.  

* Lorsque le niveau de cohérence est défini sur **Obsolescence limitée**, Cosmos DB garantit que les clients pourront toujours lire la valeur de l’écriture précédente, avec un décalage limité par la fenêtre d’obsolescence.

* Lorsque le niveau de cohérence est défini sur **fort**, la fenêtre d’obsolescence est nulle, et les clients sont assurés de lire la dernière valeur validée de l’opération d’écriture.

* Pour les trois niveaux de cohérence restants, la fenêtre d’obsolescence dépend en grande partie de votre charge de travail. Par exemple, si aucune opération d’écriture n’est effectuée sur la base de données, une opération de lecture avec un niveau de cohérence **éventuelle**, **session** ou **préfixe cohérent** risque de produire les mêmes résultats qu’une opération de lecture avec un niveau de cohérence forte.

Si votre compte Azure Cosmos est configuré avec un niveau de cohérence autre que la cohérence forte, vous pouvez déterminer la probabilité que vos clients bénéficieront de lectures fortes et cohérentes pour vos charges de travail en examinant la métrique *Probabilistically Bounded Staleness* (PBS). Cette métrique est exposée dans le portail Azure. Pour en savoir plus, consultez [Surveiller la métrique de probabilités en fonction de l’obsolescence limitée (PBS)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

La métrique de probabilités en fonction de l’obsolescence limitée montre comment la valeur éventuelle représente la cohérence éventuelle. Cette métrique fournit une idée de la fréquence à laquelle vous pouvez obtenir une cohérence plus forte que le niveau de cohérence que vous avez configuré sur votre compte Azure Cosmos. En d’autres termes, vous pouvez voir la probabilité (mesurée en millisecondes) d’obtenir des lectures fortement cohérentes pour une combinaison de régions d’écriture et de lecture.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les niveaux de cohérence, consultez les articles suivants :

* [Mappage du niveau de cohérence entre les API Cosmos DB](consistency-levels-across-apis.md)
* [Compromis entre disponibilité et performance pour différents niveaux de cohérence](consistency-levels-tradeoffs.md)
* [Gestion du jeton de session pour votre application](how-to-manage-consistency.md#utilize-session-tokens)
* [Surveiller la métrique de probabilités en fonction de l’obsolescence limitée (PBS)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)
