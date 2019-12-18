---
title: Interroger des conteneurs dans Azure Cosmos DB
description: Découvrez comment interroger des conteneurs dans Azure Cosmos DB à l’aide de requêtes dans une partition, dans plusieurs partitions et dans plusieurs partitions parallèles
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 88ebb8bb80ec3406c98b77db481994d415b04373
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872024"
---
# <a name="query-an-azure-cosmos-container"></a>Interroger un conteneur Azure Cosmos

Cet article explique comment interroger un conteneur (collection, graphe ou table) dans Azure Cosmos DB.

## <a name="in-partition-query"></a>Requête dans une partition

Quand vous interrogez des données de conteneurs, si un filtre de clé de partition est spécifié dans la requête, Azure Cosmos DB traite la requête automatiquement. Il route la requête vers les partitions qui correspondent aux valeurs de clé de partition spécifiées dans le filtre. Par exemple, la requête suivante est routée vers la partition `DeviceId`, qui contient tous les documents correspondant à la valeur de clé de partition `XMS-0001`.

```csharp
// Query using partition key into a class called, DeviceReading
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```

## <a name="cross-partition-query"></a>Requête dans plusieurs partitions

La requête suivante n’a pas de filtre sur la clé de partition (`DeviceId`). Elle est distribuée à toutes les partitions où elle est exécutée par rapport à l’index de la partition. Pour exécuter une requête sur plusieurs partitions, affectez la valeur true à `EnableCrossPartitionQuery` (ou `x-ms-documentdb-query-enablecrosspartition`  dans l’API REST).

La propriété EnablecrossPartitionQuery accepte une valeur booléenne. Si la valeur est définie sur true et si votre requête ne comprend pas de clé de partition, Azure Cosmos DB exécute la requête sur plusieurs partitions. L’exécution est réalisée en envoyant des requêtes à toutes les partitions. Pour lire les résultats des requêtes, les applications clientes doivent consommer les résultats à partir de FeedResponse et vérifier la propriété ContinuationToken. Pour lire tous les résultats, poursuivez l’itération des données jusqu’à ce que la valeur de ContinuationToken soit null. 

```csharp
// Query across partition keys into a class called, DeviceReading
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

Azure Cosmos DB prend en charge les fonctions d’agrégation COUNT, MIN, MAX et AVG sur les conteneurs en SQL. Les fonctions d’agrégation sur les conteneurs sont prises en charge à partir du kit SDK version 1.12.0 (et versions ultérieures). Les requêtes doivent comporter un opérateur d’agrégation unique et doivent inclure une valeur unique dans la projection.

## <a name="parallel-cross-partition-query"></a>Requête dans plusieurs partitions parallèles

Les kits SDK Azure Cosmos DB 1.9.0 (et versions ultérieures) prennent en charge les options d’exécution de requêtes parallèles. Les requêtes dans plusieurs partitions parallèles vous permettent d’exécuter des requêtes dans plusieurs partitions avec une faible latence. Par exemple, la requête suivante est configurée pour s’exécuter en parallèle sur plusieurs partitions.

```csharp
// Cross-partition Order By Query with parallel execution
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),  
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```

Vous pouvez gérer l’exécution de requêtes parallèles en réglant les paramètres suivants :

- **MaxDegreeOfParallelism** : définit le nombre maximal de connexions réseau simultanées aux partitions du conteneur. Si vous affectez la valeur -1 à cette propriété, le kit SDK gère le degré de parallélisme. Si  `MaxDegreeOfParallelism` n’est pas spécifié ou s’il n’a pas la valeur 0, qui correspond à la valeur par défaut, une seule connexion réseau est établie avec les partitions du conteneur.

- **MaxBufferedItemCount** : limite la latence des requêtes par rapport à l’utilisation de la mémoire côté client. Si cette option est omise ou si elle a la valeur -1, le kit SDK gère le nombre d’éléments mis en mémoire tampon durant l’exécution de requêtes parallèles.

Avec un même état de collection, une requête parallèle retourne les résultats dans le même ordre qu’une exécution en série. Quand vous exécutez une requête sur plusieurs partitions et qu’elle inclut des opérateurs de tri (ORDER BY, TOP), le kit SDK Azure Cosmos DB émet la requête en parallèle sur plusieurs partitions. Il fusionne les résultats partiellement triés côté client pour produire des résultats ordonnés globalement.

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants pour en savoir plus sur le partitionnement dans Azure Cosmos DB :

- [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Partitionnement dans Azure Cosmos DB)
- [Create a synthetic partition key](synthetic-partition-keys.md) (Créer une clé de partition synthétique)
