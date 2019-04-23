---
title: Unités de requête et débit dans Azure Cosmos DB
description: Découvrez comment spécifier et estimer les besoins en unités de requête dans Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.author: rimman
ms.openlocfilehash: 72ec8332a3363d5336fb84b3390d5e44ced0b2e5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798961"
---
# <a name="request-units-in-azure-cosmos-db"></a>Unités de requête dans Azure Cosmos DB

Avec Azure Cosmos DB, vous payez pour le débit que vous provisionnez et le stockage que vous utilisez sur une base horaire. Débit doit être configuré pour vous assurer que suffisamment de ressources système est disponibles pour votre base de données Azure Cosmos à tout moment. Vous avez besoin de suffisamment de ressources pour atteindre ou dépasser le [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

Azure Cosmos DB prend en charge de nombreuses API, notamment SQL, MongoDB, Cassandra, Gremlin et Table. Chaque API possède son propre ensemble d’opérations de base de données, qui vont de simples opérations ponctuelles de lecture et d'écriture à des requêtes complexes. Chaque opération de base de données consomme des ressources système en fonction de sa complexité. 

Le coût de toutes les opérations de base de données est normalisé par Azure Cosmos DB et est exprimé en *unités de requête* (ou unités de requête, en abrégé). Vous pouvez considérer les RU/s comme la devise du débit, qui dépend de la vitesse. Elles représentent une abstraction des ressources système (processeur, IOPS et mémoire, notamment) nécessaires pour effectuer les opérations de base de données prises en charge par Azure Cosmos DB. 

Le coût de lecture une base de connaissances 1 élément 1 unité de demande (ou 1 RU). Toutes les autres opérations de base de données sont de même affectées à un coût à l’aide d’unités de requête. Quelle que soit l’API utilisée pour interagir avec le conteneur Azure Cosmos, les coûts sont toujours mesurés en unités de requête, que l’opération de base de données soit une opération d’écriture, de lecture ou de requête.

L’illustration suivante montre l’idée générale des unités de requête :

![Les opérations de base de données consomment des unités de requête](./media/request-units/request-units.png)

Pour gérer et planifier la capacité, Azure Cosmos DB veille à ce que le nombre d’unités de requête d’une opération de base de données spécifique sur un jeu de données spécifique soit déterministe. Vous pouvez examiner l’en-tête de réponse pour suivre le nombre d’unités de requête qui sont consommées par une opération de base de données. Lorsque vous aurez la [les facteurs qui affectent les frais de RU](request-units.md#request-unit-considerations) et exigences de débit de votre application, vous pouvez exécuter votre application à moindre coût.

Le nombre d’unités de requête d’une application est approvisionné par seconde et par incréments de 100 RU/s. Si vous souhaitez faire évoluer le débit approvisionné pour votre application, vous pouvez à tout moment augmenter ou diminuer le nombre d’unités de requête, Vous pouvez faire évoluer dans incrémente ou décrémente de 100 unités de requête. soit programmatiquement soit sur le Portail Azure. Vous êtes facturé sur une base horaire.

Vous pouvez configurer le débit selon deux niveaux de granularité distincts : 

* **Conteneurs** : Pour plus d’informations, voir [Approvisionner le débit sur un conteneur Azure Cosmos](how-to-provision-container-throughput.md).
* **Bases de données**: Pour plus d’informations, voir [Approvisionner le débit sur une base de données Azure Cosmos](how-to-provision-database-throughput.md).

## <a name="request-unit-considerations"></a>Considérations relatives aux unités de requête

Lorsque vous estimez le nombre d’unités de requête par seconde à approvisionner, tenez compte des facteurs suivants :

* **Taille de l’élément** : plus la taille d’un élément augmente, plus le nombre d'unités de requête consommées pour le lire ou l’écrire augmente.

* **Indexation de l’élément** : Par défaut, chaque élément est indexé automatiquement. Moins d’unités de requête sont consommées si vous choisissez de ne pas indexer tous les éléments d’un conteneur.

* **Nombre de propriétés de l’élément** : En supposant que l’indexation par défaut se trouve sur toutes les propriétés, le nombre d’unités de requête consommées pour écrire qu'un élément augmente en même temps que l’élément propriété count.

* **Propriétés indexées** : Une stratégie d’indexation sur chaque conteneur détermine quelles propriétés sont indexées par défaut. Pour réduire la consommation d’unités de requête des opérations d’écriture, limitez le nombre de propriétés indexées.

* **Cohérence des données** : les niveaux de cohérence de type fort et obsolescence limitée consomment approximativement deux fois plus d'unités de requête lors des opérations de lecture que les niveaux de cohérence de type flexible.

* **Modèles de requête** : la complexité d’une requête a une incidence sur le nombre d’unités de requête consommées pour une opération. Les facteurs qui influent sur le coût des opérations de requête sont les suivants : 
    
    - Le nombre de résultats de requête
    - Le nombre de prédicats
    - La nature des prédicats
    - Le nombre de fonctions définies par l’utilisateur
    - La taille de la source de données
    - La taille du jeu de résultats
    - Projections

  Azure Cosmos DB garantit qu’une même requête portant sur les mêmes données coûte toujours le même nombre d’unités de requête en cas d’exécutions répétées.

* **Utilisation de scripts** : comme avec les requêtes, les procédures stockées et les déclencheurs consomment plus ou moins d'unités de requête en fonction de la complexité des opérations effectuées. Lorsque vous développez votre application, inspecter la [en-tête frais de requête](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query) pour mieux comprendre la capacité de RU chaque opération consomme.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [approvisionner le débit sur des conteneurs et bases de données Azure Cosmos](set-throughput.md).
* En savoir plus sur les [partitions logiques](partition-data.md).
* Découvrez comment [mettre à l’échelle le débit approvisionné au niveau global](scaling-throughput.md).
* Découvrez comment [approvisionner le débit sur un conteneur Azure Cosmos](how-to-provision-container-throughput.md).
* Découvrez comment [approvisionner le débit sur une base de données Azure Cosmos](how-to-provision-database-throughput.md).
* Découvrez comment [de trouver les frais d’unités de demande pour une opération](find-request-unit-charge.md).
* Découvrez comment [optimiser le coût du débit approvisionné dans Azure Cosmos DB](optimize-cost-throughput.md).
* Découvrez comment [optimiser les lectures et écritures de coût dans Azure Cosmos DB](optimize-cost-reads-writes.md).
* Découvrez comment [optimiser le coût de la requête dans Azure Cosmos DB](optimize-cost-queries.md).
