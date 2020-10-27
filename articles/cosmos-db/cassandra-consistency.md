---
title: Niveaux de cohérence Apache Cassandra et Azure Cosmos DB
description: Niveaux de cohérence Apache Cassandra et Azure Cosmos DB.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: 155e1ed5b089c16887c845c94f36c9c6d9f0af6e
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92283023"
---
# <a name="apache-cassandra-and-azure-cosmos-db-consistency-levels"></a>Niveaux de cohérence Apache Cassandra et Azure Cosmos DB

Contrairement à Azure Cosmos DB, Apache Cassandra ne fournit pas en mode natif des garanties de cohérence précises. Au lieu de cela, Apache Cassandra fournit un niveau de cohérence d’écriture et un niveau de cohérence de lecture, pour permettre les compromis de haute disponibilité, de cohérence et de latence. Lors de l’utilisation de l’API Cassandra Azure Cosmos DB :

* Le niveau de cohérence d’écriture d’Apache Cassandra est mappé au niveau de cohérence par défaut configuré sur votre compte Azure Cosmos. La cohérence d’une opération d’écriture ne peut pas être changée en fonction de la demande.

* Azure Cosmos DB mappe de manière dynamique le niveau de cohérence de lecture spécifié par le pilote client Cassandra à l’un des niveaux de cohérence Azure Cosmos DB configurés dynamiquement sur une requête de lecture.

## <a name="mapping-consistency-levels"></a>Correspondance des niveaux de cohérence

Le tableau suivant illustre le mappage des niveaux de cohérence Cassandra en mode natif aux niveaux de cohérence d’Azure Cosmos DB lors de l’utilisation de l’API Cassandra :  

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" alt-text="Mappage du modèle de cohérence Cassandra" lightbox="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" :::

Si votre compte Azure Cosmos est configuré avec un niveau de cohérence autre que la cohérence forte, vous pouvez déterminer la probabilité que vos clients bénéficieront de lectures fortes et cohérentes pour vos charges de travail en examinant la métrique *Probabilistically Bounded Staleness* (PBS). Cette métrique est exposée dans le portail Azure. Pour en savoir plus, consultez [Surveiller la métrique de probabilités en fonction de l’obsolescence limitée (PBS)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

La métrique de probabilités en fonction de l’obsolescence limitée montre comment la valeur éventuelle représente la cohérence éventuelle. Cette métrique fournit une idée de la fréquence à laquelle vous pouvez obtenir une cohérence plus forte que le niveau de cohérence que vous avez configuré sur votre compte Azure Cosmos. En d’autres termes, vous pouvez voir la probabilité (mesurée en millisecondes) d’obtenir des lectures fortement cohérentes pour une combinaison de régions d’écriture et de lecture.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les niveaux de distribution et de cohérence globaux pour Azure Cosmos DB :

* [Vue d’ensemble de la distribution mondiale](distribute-data-globally.md)
* [Vue d’ensemble d’un niveau de cohérence](consistency-levels.md)
* [Haute disponibilité](high-availability.md)
