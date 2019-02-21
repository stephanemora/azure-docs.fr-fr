---
title: Unités de requête et débit dans Azure Cosmos DB
description: Découvrez comment spécifier et estimer les besoins en unités de requête dans Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: rimman
ms.openlocfilehash: 3801c19fbef70bf5d67670c4d9acc950291853e6
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2019
ms.locfileid: "55990153"
---
# <a name="request-units-in-azure-cosmos-db"></a>Unités de requête dans Azure Cosmos DB

Avec Azure Cosmos DB, vous payez pour le débit que vous provisionnez et le stockage que vous utilisez sur une base horaire. Le débit doit être approvisionné de sorte qu’il y ait assez de ressources système disponibles en permanence pour la base de données Azure Cosmos. L’objectif est d’atteindre ou de dépasser le contrat de niveau de service Azure Cosmos DB.

Azure Cosmos DB prend en charge de nombreuses API, notamment SQL, MongoDB, Cassandra, Gremlin et Table. Chaque API possède son propre ensemble d’opérations de base de données, qui vont de simples opérations ponctuelles de lecture et d'écriture à des requêtes complexes. Chaque opération de base de données consomme des ressources système en fonction de sa complexité. 

Le coût de toutes les opérations de base de données, normalisé par Azure Cosmos DB, est exprimé en unités de requête (RU). Le coût de lecture d'un élément de 1 Ko est d'une unité de requête. Toutes les autres opérations de base de données se voient aussi attribuer un coût en unités de requête. Quelle que soit l’API utilisée pour interagir avec le conteneur Azure Cosmos, les coûts sont toujours mesurés en unités de requête, que l’opération de base de données soit une opération d’écriture, de lecture ou de requête.

Vous pouvez considérer les RU/s comme la devise du débit, qui dépend de la vitesse. Elles représentent une abstraction des ressources système (processeur, IOPS et mémoire, notamment) nécessaires pour effectuer les opérations de base de données prises en charge par Azure Cosmos DB. L’illustration suivante montre les unités de requête consommées par différentes opérations de base de données :

![Les opérations de base de données consomment des unités de requête](./media/request-units/request-units.png)

Pour gérer et planifier la capacité, Azure Cosmos DB veille à ce que le nombre d’unités de requête d’une opération de base de données spécifique sur un jeu de données spécifique soit déterministe. Examinez l’en-tête de réponse pour suivre le nombre d’unités de requête consommées par une opération de base de données. Une fois que vous aurez compris quels facteurs ont un impact sur les frais d’unités de requête et cerné les besoins de débit de votre application, vous pourrez exécuter votre application de la manière la plus rentable possible.

Les frais sont facturés à l’heure. Le nombre d’unités de requête d’une application est approvisionné par seconde et par incréments de 100 RU/s. Si vous souhaitez faire évoluer le débit approvisionné pour votre application, vous pouvez à tout moment augmenter ou diminuer le nombre d’unités de requête, par incréments ou décréments de 100 unités de requête, soit programmatiquement soit sur le Portail Azure.

Vous pouvez configurer le débit selon deux niveaux de granularité distincts : 

* **Conteneurs**. Pour plus d’informations, voir [Approvisionner le débit sur un conteneur Azure Cosmos](how-to-provision-container-throughput.md).
* **Bases de données**. Pour plus d’informations, voir [Approvisionner le débit sur une base de données Azure Cosmos](how-to-provision-database-throughput.md).

## <a name="request-unit-considerations"></a>Considérations relatives aux unités de requête

Lorsque vous estimez le nombre d’unités de requête par seconde à approvisionner, tenez compte des facteurs suivants :

* **Taille de l’élément** : plus la taille d’un élément augmente, plus le nombre d'unités de requête consommées pour le lire ou l’écrire augmente.

* **Indexation de l’élément** : Par défaut, chaque élément est indexé automatiquement. Moins d’unités de requête sont consommées si vous choisissez de ne pas indexer tous les éléments d’un conteneur.

* **Nombre de propriétés de l’élément** : sur la base d'une indexation par défaut de toutes les propriétés, le nombre d'unités de requête consommées pour écrire un élément augmente avec le nombre de propriétés de l'élément.

* **Propriétés indexées** : Une stratégie d’indexation sur chaque conteneur détermine quelles propriétés sont indexées par défaut. Pour réduire la consommation d’unités de requête des opérations d’écriture, limitez le nombre de propriétés indexées.

* **Cohérence des données** : les niveaux de cohérence de type fort et obsolescence limitée consomment approximativement deux fois plus d'unités de requête lors des opérations de lecture que les niveaux de cohérence de type flexible.

* **Modèles de requête** : la complexité d’une requête a une incidence sur le nombre d’unités de requête consommées pour une opération. Les facteurs qui influent sur le coût des opérations de requête sont les suivants : 
    
    - le nombre de résultats de la requête ;
    - le nombre de prédicats ;
    - la nature des prédicats ;
    - le nombre de fonctions définies par l’utilisateur ;
    - la taille des données sources ;
    - la taille du jeu de résultats ;
    - les projections.

  Azure Cosmos DB garantit qu’une même requête portant sur les mêmes données coûte toujours le même nombre d’unités de requête en cas d’exécutions répétées.

* **Utilisation de scripts** : comme avec les requêtes, les procédures stockées et les déclencheurs consomment plus ou moins d'unités de requête en fonction de la complexité des opérations effectuées. Lors du développement de votre application, inspectez l’en-tête des frais de requêtes pour mieux comprendre la capacité en unités de requête consommée par opération.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [approvisionner le débit des conteneurs et bases de données Azure Cosmos](set-throughput.md).
* Découvrez les [partitions logiques](partition-data.md).
* Découvrez comment [mettre à l’échelle le débit approvisionné au niveau global](scaling-throughput.md).
* Découvrez comment [approvisionner le débit sur un conteneur Azure Cosmos](how-to-provision-container-throughput.md).
* Découvrez comment [approvisionner le débit sur une base de données Azure Cosmos](how-to-provision-database-throughput.md).
