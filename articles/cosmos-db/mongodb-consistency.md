---
title: Mappage des niveaux de cohérence pour Azure Cosmos DB API pour MongoDB
description: Mappage des niveaux de cohérence pour Azure Cosmos DB API pour MongoDB.
author: sivethe
ms.author: sivethe
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: 19dcc0f7605ae771e2ff826299413eaa073f212b
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92283024"
---
# <a name="consistency-levels-for-azure-cosmos-db-and-the-api-for-mongodb"></a>Niveaux de cohérence pour Azure Cosmos DB et l’API pour MongoDB

Contrairement à Azure Cosmos DB, MongoDB en mode natif ne fournit pas de garanties de cohérence précises. Au lieu de cela, MongoDB en mode natif permet aux utilisateurs de configurer les garanties de cohérence suivantes : problème d’écriture, problème de lecture et directive isMaster - pour diriger les opérations de lecture vers les réplicas principaux ou secondaires dans l’optique d’atteindre le niveau de cohérence souhaité.

Lorsque vous utilisez l’API Azure Cosmos DB pour MongoDB, le pilote MongoDB traite votre région d’écriture en tant que réplica principal, et toutes les autres régions sont des réplicas en lecture. Vous pouvez choisir la région associée à votre compte Azure Cosmos en tant que réplica principal.

> [!NOTE]
> Le modèle de cohérence par défaut pour Azure Cosmos DB est Session. Session est un modèle de cohérence centré sur le client qui n’est pas pris en charge nativement par Cassandra ni MongoDB. Pour plus d’informations sur le modèle de cohérence à choisir, consultez [Niveaux de cohérence dans Azure Cosmos DB](consistency-levels.md).

Lors de l’utilisation de l’API Azure Cosmos DB pour MongoDB :

* Le problème d’écriture est mappé au niveau de cohérence par défaut configuré sur votre compte Azure Cosmos.

* Azure Cosmos DB mappe de manière dynamique le problème de lecture spécifié par le pilote client MongoDB à l’un des niveaux de cohérence Azure Cosmos DB configurés dynamiquement sur une requête de lecture.  

* Vous pouvez annoter une région associée à votre compte Azure Cosmos comme étant « Primaire », en la désignant comme première région accessible en écriture. 

## <a name="mapping-consistency-levels"></a>Correspondance des niveaux de cohérence

Le tableau suivant illustre le mappage des problèmes d’écriture/lecture Cassandra en mode natif aux niveaux de cohérence d’Azure Cosmos lors de l’utilisation de l’API Azure Cosmos DB pour MongoDB :

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png" alt-text="Mappage du modèle de cohérence MongoDB" lightbox= "./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png":::

Si votre compte Azure Cosmos est configuré avec un niveau de cohérence autre que la cohérence forte, vous pouvez déterminer la probabilité que vos clients bénéficieront de lectures fortes et cohérentes pour vos charges de travail en examinant la métrique *Probabilistically Bounded Staleness* (PBS). Cette métrique est exposée dans le portail Azure. Pour en savoir plus, consultez [Surveiller la métrique de probabilités en fonction de l’obsolescence limitée (PBS)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

La métrique de probabilités en fonction de l’obsolescence limitée montre comment la valeur éventuelle représente la cohérence éventuelle. Cette métrique fournit une idée de la fréquence à laquelle vous pouvez obtenir une cohérence plus forte que le niveau de cohérence que vous avez configuré sur votre compte Azure Cosmos. En d’autres termes, vous pouvez voir la probabilité (mesurée en millisecondes) d’obtenir des lectures fortement cohérentes pour une combinaison de régions d’écriture et de lecture.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les niveaux de distribution et de cohérence globaux pour Azure Cosmos DB :

* [Vue d’ensemble de la distribution mondiale](distribute-data-globally.md)
* [Vue d’ensemble d’un niveau de cohérence](consistency-levels.md)
* [Haute disponibilité](high-availability.md)
