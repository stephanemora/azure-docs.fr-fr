---
title: Interroger des conteneurs dans Azure Cosmos DB
description: Découvrez comment interroger des conteneurs dans Azure Cosmos DB
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 11c68b61802f6c7b3755da71c176ea777f171e4c
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53409834"
---
# <a name="query-containers-in-azure-cosmos-db"></a>Interroger des conteneurs dans Azure Cosmos DB

Cet article explique comment interroger un conteneur (collection, graphique, table) dans Azure Cosmos DB.

## <a name="in-partition-query"></a>Requête dans une partition

Lorsque vous interrogez des données dans des conteneurs, si un filtre de clé de partition est spécifié dans la requête, , Cosmos DB achemine automatiquement la requête vers les partitions correspondant aux valeurs de clé de partition spécifiées dans le filtre. Par exemple, la requête suivante est acheminée vers la partition DeviceId qui contient tous les documents correspondant à la valeur de clé de partition « XMS-0001 ».

```csharp
// Query using partition key into a class called, DeviceReading
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```

## <a name="cross-partition-query"></a>Requête dans plusieurs partitions

Pour la requête suivante, la clé de partition (DeviceId) n’a pas de filtre. La requête est donc distribuée à toutes les partitions où elle est exécutée sur l’index de la partition. Pour exécuter une requête dans plusieurs partitions, définissez **EnableCrossPartitionQuery** sur true (ou x-ms-documentdb-query-enablecrosspartition dans l’API REST).

```csharp
// Query across partition keys into a class called, DeviceReading
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

Cosmos DB prend en charge les fonctions d’agrégation COUNT, MIN, MAX et AVG sur les conteneurs à l’aide de SQL. Les fonctions d’agrégation sur les conteneurs sont prises en charge à partir du Kit de développement logiciel (SDK) version 1.12.0 et versions ultérieures. Les requêtes doivent comporter un opérateur d’agrégation unique et doivent inclure une valeur unique dans la projection.

## <a name="parallel-cross-partition-query"></a>Requête dans plusieurs partitions parallèles

Les Kits de développement logiciel (SDK) Cosmos DB 1.9.0 et versions ultérieures prennent en charge les options d’exécution de requêtes parallèles.  Les requêtes dans plusieurs partitions parallèles vous permettent d’exécuter des requêtes dans plusieurs partitions avec une faible latence. Par exemple, la requête suivante est configurée pour s’exécuter en parallèle sur plusieurs partitions.

```csharp
// Cross-partition Order By Query with parallel execution
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),  
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```

Vous pouvez gérer l’exécution de requêtes parallèles en réglant les paramètres suivants :

- **MaxDegreeOfParallelism** : définit le nombre maximal de connexions réseau simultanées aux partitions du conteneur. Si vous définissez cette propriété sur -1, le degré de parallélisme est géré par le SDK. Si la propriété MaxDegreeOfParallelism n’est pas spécifiée ou définie sur 0, qui est la valeur par défaut, il n’y a qu’une seule connexion réseau aux partitions du conteneur.

- **MaxBufferedItemCount** : limite la latence des requêtes par rapport à l’utilisation de la mémoire côté client. Si vous omettez cette option ou si vous la définissez sur -1, le nombre d’éléments mis en mémoire tampon pendant l’exécution de requêtes parallèles est géré par le Kit de développement logiciel (SDK).

Avec un même état de collection, une requête parallèle renvoie les résultats dans l’ordre d’exécution en série. Lorsque vous exécutez une requête sur plusieurs partitions qui inclut des opérateurs de tri (ORDER BY et/ou TOP), le Kit de développement logiciel (SDK) d’Azure Cosmos DB émet la requête en parallèle sur plusieurs partitions et fusionne les résultats partiellement triés côté client pour produire des résultats classés globalement.

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants pour en savoir plus sur le partitionnement dans Cosmos DB :

- [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Partitionnement dans Azure Cosmos DB)
- [Create a synthetic partition key](synthetic-partition-keys.md) (Créer une clé de partition synthétique)
