---
title: Migrer des conteneurs de Azure Cosmos DB non partitionnée à des conteneurs partitionnés
description: Apprenez à migrer tous les conteneurs non partitionnée existantes dans des conteneurs partitionnés.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 8ba9489496a8f9e3703702e344684b4028a002cc
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241938"
---
# <a name="migrate-non-partitioned-containers-to-partitioned-containers"></a>Migrer des conteneurs non partitionnée à des conteneurs partitionnés

Azure Cosmos DB prend en charge la création de conteneurs sans une clé de partition. Actuellement, vous pouvez créer des conteneurs non partitionnée à l’aide d’Azure CLI et SDK Azure Cosmos DB (.Net, Java, NodeJs) qui ont une version inférieure ou égale à 2.x. Impossible de créer les conteneurs non partitionnée à l’aide du portail Azure. Toutefois, ces conteneurs non partitionnées ne sont pas élastique et rectifiées la capacité de stockage de la limite de 10 Go et le débit de 10 K ur/s.

Les conteneurs non partitionnées sont héritées, et vous devez migrer vos conteneurs non partitionnée existantes vers des conteneurs partitionnés à stockage à l’échelle et de débit. Azure Cosmos DB fournit un mécanisme défini par le système pour migrer vos conteneurs non partitionnée à des conteneurs partitionnés. Ce document explique comment tous les conteneurs non partitionnée existantes sont migrées automatiquement dans des conteneurs partitionnés. Vous pouvez tirer parti de la fonctionnalité de migration automatique uniquement si vous utilisez la version V3 de kits de développement logiciel dans toutes les langues.

> [!NOTE] 
> Actuellement, vous ne pouvez pas migrer des comptes Azure Cosmos DB MongoDB et API Gremlin à l’aide de la procédure décrite dans ce document. 

## <a name="migrate-container-using-the-system-defined-partition-key"></a>Migrer de conteneur à l’aide de la clé de partition définies par le système

Pour prendre en charge la migration, Azure Cosmos DB définit une clé de partition définies par le système nommée `/_partitionkey` sur tous les conteneurs qui n’ont pas une clé de partition. Vous ne pouvez pas modifier la définition de clé de partition après avoir migrent les conteneurs. Par exemple, la définition d’un conteneur qui est migré vers un conteneur partitionné sera comme suit : 

```json
{
    "Id": "CollId" 
  "partitionKey": {
    "paths": [
      "/_partitionKey"
    ],
    "kind": "Hash"
  },
}
```
 
Une fois le conteneur est migré, vous pouvez créer des documents en remplissant le `_partitionKey` propriété, ainsi que les autres propriétés du document. Le `_partitionKey` propriété représente la clé de partition de vos documents. 

Choix de la clé de partition appropriée est important d’utiliser le débit approvisionné de façon optimale. Pour plus d’informations, consultez [comment choisir une clé de partition](partitioning-overview.md) article. 

> [!NOTE]
> Vous pouvez tirer parti de la clé de partition définie système uniquement si vous utilisez la version plus récente/V3 de kits de développement logiciel dans toutes les langues.

L’exemple suivant montre un exemple de code pour créer un document avec la clé de partition définie système et de lire ce document :

**Représentation JSON du document**

```csharp
DeviceInformationItem = new DeviceInformationItem
{
   "id": "elevator/PugetSound/Building44/Floor1/1",
   "deviceId": "3cf4c52d-cc67-4bb8-b02f-f6185007a808",
   "_partitionKey": "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
} 

public class DeviceInformationItem
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }

    [JsonProperty(PropertyName = "deviceId")]
    public string DeviceId { get; set; }

    [JsonProperty(PropertyName = "_partitionKey")]
    public string PartitionKey {get {return this.DeviceId; set; }
}

CosmosContainer migratedContainer = database.Containers["testContainer"];

DeviceInformationItem deviceItem = new DeviceInformationItem() {
  Id = "1234", 
  DeviceId = "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
} 

CosmosItemResponse<DeviceInformationItem > response = 
  await migratedContainer.Items.CreateItemAsync(
    deviceItem.PartitionKey, 
    deviceItem
  );

// Read back the document providing the same partition key
CosmosItemResponse<DeviceInformationItem> readResponse = 
  await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
    partitionKey:deviceItem.PartitionKey, 
    id: device.Id
  ); 

```

Pour obtenir un exemple complet, consultez la [exemples .net](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples) référentiel GitHub. 
                      
## <a name="migrate-the-documents"></a>Migrer les documents

Bien que la définition de conteneur a été améliorée avec une propriété de clé de partition, les documents au sein du conteneur ne sont pas automatiquement migrés. Ce qui signifie que la propriété de clé de partition système `/_partitionKey` chemin d’accès n’est pas automatiquement ajouté pour les documents existants. Vous devez repartitionner les documents existants en lisant les documents qui ont été créées sans une clé de partition et les réécrire avec `_partitionKey` propriété dans les documents. 

## <a name="access-documents-that-dont-have-a-partition-key"></a>Accéder aux documents qui n’ont pas une clé de partition

Applications peuvent accéder aux documents existants qui n’ont pas une clé de partition à l’aide de la propriété système spécial appelée « CosmosContainerSettings.NonePartitionKeyValue », c’est la valeur des documents non migrés. Vous pouvez utiliser cette propriété dans toutes les opérations CRUD et de la requête. L’exemple suivant montre un exemple pour lire un Document unique à partir de la NonePartitionKey. 

```csharp
CosmosItemResponse<DeviceInformationItem> readResponse = 
await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
  partitionKey: CosmosContainerSettings.NonePartitionKeyValue, 
  id: device.Id
); 

```

Pour obtenir l’exemple complet sur la façon de repartitionner les documents, consultez le [exemples .net](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples) référentiel GitHub. 

## <a name="compatibility-with-sdks"></a>Compatibilité avec les kits de développement logiciel

Ancienne version du SDK Azure Cosmos DB telles que V2.x.x et V1.x.x ne prennent pas en charge la propriété de clé de partition définies par le système. Par conséquent, lorsque vous lisez la définition de conteneur à partir d’un SDK plus ancien, il ne contient pas une définition de clé de partition et ces conteneurs seront comporte exactement comme auparavant. Les applications qui sont générées avec l’ancienne version de kits de développement logiciel continuent de fonctionner avec non partitionnée en l’état sans aucune modification. 

Si un conteneur migré est consommé par la version/V3 dernière version du SDK et commencer à remplir la clé de partition définies par le système dans les nouveaux documents, vous ne pouvez pas accéder (read, update, delete, requête) ces documents plus à partir de kits de développement logiciel plus anciens.

## <a name="next-steps"></a>Étapes suivantes

* [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Partitionnement dans Azure Cosmos DB)
* [Unités de requête dans Azure Cosmos DB](request-units.md)
* [Provisionner le débit sur les conteneurs et les bases de données](set-throughput.md)
* [Utiliser un compte Azure Cosmos](account-overview.md)