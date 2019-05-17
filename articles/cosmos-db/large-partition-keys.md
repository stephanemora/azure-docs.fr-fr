---
title: Créer les conteneurs Azure Cosmos avec clé de partition volumineuse à l’aide du portail Azure et les différents kits SDK.
description: Découvrez comment créer un conteneur dans Azure Cosmos DB avec la clé de partition volumineuse à l’aide du portail Azure et les différents kits de développement logiciel.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: 322d5630daeedaa671f994b1374f15f655811de5
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65796085"
---
# <a name="create-containers-with-large-partition-key"></a>Créer des conteneurs avec clé de partition volumineuse

Azure Cosmos DB utilise le schéma de partitionnement par hachage pour obtenir la mise à l’échelle horizontale des données. Tous les conteneurs Azure Cosmos créés avant 2019 3 mai utilisent une fonction de hachage qui calcule le hachage basé sur les 100 premiers octets de la clé de partition. S’il existe plusieurs clés de partition qui ont les mêmes 100 premiers octets, ces partitions logiques sont considérées comme la même partition logique par le service. Cela peut entraîner des problèmes tels que le quota de taille de partition qui est incorrect et appliquées sur les clés de partition des index uniques. Les clés de grande partition sont introduites pour résoudre ce problème. Azure Cosmos DB maintenant prend en charge les clés de grande partition avec des valeurs jusqu'à 2 Ko. 

Les clés de partition de grandes clés sont prises en charge à l’aide de la fonctionnalité d’une version améliorée de la fonction de hachage, ce qui peut générer un hachage unique à partir de la grande partition jusqu'à 2 Ko. Cette version de hachage est également recommandée pour les scénarios avec la cardinalité de clé de partition haute, quelle que soit la taille de la clé de partition. Une cardinalité de clé de partition est définie comme le nombre de partitions de logiques uniques, par exemple dans l’ordre de ~ 30000 des partitions logiques dans un conteneur. Cet article décrit comment créer un conteneur avec une clé de partition volumineuse à l’aide du portail Azure et les différents kits de développement logiciel. 

## <a name="create-a-large-partition-key-net-sdk-v2"></a>Créer une clé de partition volumineuse (.Net SDK V2)

Lorsque vous utilisez le kit SDK .net pour créer un conteneur avec la clé de partition volumineuse, vous devez spécifier le `PartitionKeyDefinitionVersion.V2` propriété. L’exemple suivant montre comment spécifier la propriété de Version dans l’objet PartitionKeyDefinition et affectez-lui la valeur PartitionKeyDefinitionVersion.V2 :

```csharp
DocumentCollection collection = await newClient.CreateDocumentCollectionAsync(
database,
     new DocumentCollection
        {
           Id = Guid.NewGuid().ToString(),
           PartitionKey = new PartitionKeyDefinition
           {
             Paths = new Collection<string> {"/longpartitionkey" },
             Version = PartitionKeyDefinitionVersion.V2
           }
         },
      new RequestOptions { OfferThroughput = 400 });
```

## <a name="create-a-large-partition-key-azure-portal"></a>Créer une clé de partition volumineuse (portail Azure) 

Pour créer une clé de partition volumineuse, alors que vous créez un conteneur à l’aide du portail Azure, activez la **ma clé de partition est supérieure à 100 octets** option. Par défaut, tous les nouveaux conteneurs a été sélectionnés en utilisant les clés de grande partition. Désélectionnez la case à cocher si vous n’avez pas besoin des clés de partition volumineuse ou si vous avez des applications qui s’exécutent sur les kits de développement logiciel version antérieure à 1,18.

![Créer des clés de partition volumineuse à l’aide du portail Azure](./media/large-partition-keys/large-partition-key-with-portal.png)


## <a name="supported-sdk-versions"></a>Versions prises en charge du kit de développement logiciel (SDK)

Les clés de partition volumineux sont pris en charge avec les versions minimales suivantes de kits de développement logiciel :

|Type du Kit de développement logiciel  | Version minimale   |
|---------|---------|
|.Net     |    1.18     |
|Synchronisation de Java     |   2.4.0      |
|Java Async   |  2.5.0        |
| API REST | version supérieure à `2017-05-03` à l’aide de la `x-ms-version` en-tête de demande.|
 
## <a name="next-steps"></a>Étapes suivantes

* [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Partitionnement dans Azure Cosmos DB)
* [Unités de requête dans Azure Cosmos DB](request-units.md)
* [Provisionner le débit sur les conteneurs et les bases de données](set-throughput.md)
* [Utiliser un compte Azure Cosmos](account-overview.md)


