---
title: Unités de requête en tant qu’unités de débit et de performances dans Azure Cosmos DB
description: Découvrez comment spécifier et estimer les besoins en unités de requête dans Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 6831cb3f39c25eb69d16300156f456980cf57fa0
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88604817"
---
# <a name="request-units-in-azure-cosmos-db"></a>Unités de requête dans Azure Cosmos DB

Azure Cosmos DB prend en charge de nombreuses API, notamment SQL, MongoDB, Cassandra, Gremlin et Table. Chaque API possède son propre ensemble d’opérations de base de données, qui vont de simples opérations ponctuelles de lecture et d'écriture à des requêtes complexes. Chaque opération de base de données consomme des ressources système en fonction de sa complexité.

Le coût de toutes les opérations de base de données, normalisé par Azure Cosmos DB, est exprimé en *unités de requête* (RU). Les RU correspondent en quelque sorte à une devise de performances, faisant abstraction des ressources système (UC, IOPS, mémoire, etc.) requises pour effectuer les opérations de base de données prises en charge par Azure Cosmos DB.

Le coût de lecture ponctuelle (par exemple, l’extraction d’un seul élément par son ID et sa valeur de clé de partition) pour un élément de 1 Ko équivaut à 1 unité de requête (1 RU). Toutes les autres opérations de base de données se voient aussi attribuer un coût en unités de requête. Quelle que soit l’API utilisée pour interagir avec le conteneur Azure Cosmos, les coûts sont toujours mesurés en unités de requête, Que l’opération de base de données soit une opération d’écriture, de lecture de point ou de requête, les coûts sont toujours mesurés en unités de requête.

L’image suivante illustre l’idée générale des unités de requête :

:::image type="content" source="./media/request-units/request-units.png" alt-text="Les opérations de base de données consomment des unités de requête" border="false":::

Pour gérer et planifier la capacité, Azure Cosmos DB veille à ce que le nombre d’unités de requête d’une opération de base de données spécifique sur un jeu de données spécifique soit déterministe. Vous pouvez examiner l’en-tête de réponse pour suivre le nombre d’unités de requête consommées par une opération de base de données. Une fois que vous avez compris [quels facteurs ont un impact sur les frais d’unités de requête](request-units.md#request-unit-considerations) et cerné les besoins de débit de l’application, vous pouvez exécuter l’application de la manière la plus rentable possible.

Le type de compte Azure Cosmos que vous utilisez détermine la façon dont les RU consommées sont facturées :

- En mode [débit approvisionné](set-throughput.md), vous approvisionnez le nombre de RU/s pour votre application, par incréments de 100 RU/s. Pour mettre à l’échelle le débit approvisionné pour votre application, vous pouvez à tout moment augmenter ou diminuer le nombre d’unités de requête par incréments ou décréments de 100 RU. soit programmatiquement soit sur le Portail Azure. Vous êtes facturé sur une base horaire pour la quantité de RUs par seconde que vous avez approvisionnée. Vous pouvez configurer le débit selon deux niveaux de granularité distincts :
  - **Conteneurs** : Pour plus d’informations, voir [Approvisionner le débit sur un conteneur Azure Cosmos](how-to-provision-container-throughput.md).
  - **Bases de données** : Pour plus d’informations, voir [Approvisionner le débit sur une base de données Azure Cosmos](how-to-provision-database-throughput.md).
- En mode [serverless](serverless.md), vous n’avez pas besoin d’approvisionner un débit lors de la création de ressources dans votre compte Azure Cosmos. À la fin de votre période de facturation, vous êtes facturé pour la quantité d’unités de requête consommées par vos opérations de base de données.

## <a name="request-unit-considerations"></a>Considérations relatives aux unités de requête

Pendant que vous estimez le nombre de RU que votre charge de travail consomme, tenez compte des facteurs suivants :

* **Taille de l’élément** : plus la taille d’un élément augmente, plus le nombre d'unités de requête consommées pour le lire ou l’écrire augmente.

* **Indexation de l’élément** : Par défaut, chaque élément est indexé automatiquement. Moins d’unités de requête sont consommées si vous choisissez de ne pas indexer tous les éléments d’un conteneur.

* **Nombre de propriétés de l’élément** : en supposant que l’indexation par défaut concerne toutes les propriétés, le nombre d’unités de requête consommées pour écrire un élément augmente avec le nombre de propriétés de l’élément.

* **Propriétés indexées** : Une stratégie d’indexation sur chaque conteneur détermine quelles propriétés sont indexées par défaut. Pour réduire la consommation d’unités de requête des opérations d’écriture, limitez le nombre de propriétés indexées.

* **Cohérence des données** : les niveaux de cohérence de type fort et obsolescence limitée consomment approximativement deux fois plus d'unités de requête lors des opérations de lecture que les niveaux de cohérence de type flexible.

* **Type des lectures** : Les lectures ponctuelles sont beaucoup plus économiques en RU que les requêtes.

* **Modèles de requête** : la complexité d’une requête a une incidence sur le nombre d’unités de requête consommées pour une opération. Les facteurs qui influent sur le coût des opérations de requête sont les suivants : 
    
    - le nombre de résultats de la requête ;
    - le nombre de prédicats ;
    - la nature des prédicats ;
    - le nombre de fonctions définies par l’utilisateur ;
    - la taille des données sources ;
    - la taille du jeu de résultats.
    - Projections

  Azure Cosmos DB garantit qu’une même requête portant sur les mêmes données coûte toujours le même nombre d’unités de requête en cas d’exécutions répétées.

* **Utilisation de scripts** : comme avec les requêtes, les procédures stockées et les déclencheurs consomment plus ou moins d'unités de requête en fonction de la complexité des opérations effectuées. Lors du développement de l’application, inspectez [l’en-tête des frais de requêtes](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query) pour mieux comprendre la capacité en unités de requête consommée par opération.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [approvisionner le débit sur des conteneurs et bases de données Azure Cosmos](set-throughput.md).
* En savoir plus sur le [mode serverless sur Azure Cosmos DB](serverless.md).
* En savoir plus sur les [partitions logiques](partition-data.md).
* Découvrez comment [mettre à l’échelle le débit approvisionné au niveau global](scaling-throughput.md).
* Découvrez comment [approvisionner le débit sur un conteneur Azure Cosmos](how-to-provision-container-throughput.md).
* Découvrez comment [approvisionner le débit sur une base de données Azure Cosmos](how-to-provision-database-throughput.md).
* Découvrez comment [rechercher les frais d’unités de requête pour une opération](find-request-unit-charge.md).
* Découvrez comment [optimiser le coût du débit approvisionné dans Azure Cosmos DB](optimize-cost-throughput.md).
* Découvrez comment [optimiser les coûts de lecture et d’écriture dans Azure Cosmos DB](optimize-cost-reads-writes.md).
* Découvrez comment [optimiser les coûts de requête dans Azure Cosmos DB](optimize-cost-queries.md).
* Découvrez comment [utiliser des mesures pour surveiller le débit](use-metrics.md).
