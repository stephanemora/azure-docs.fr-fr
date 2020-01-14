---
title: Implémenter une synchronisation personnalisée dans Microsoft Azure Cosmos DB
description: Découvrez comment implémenter une synchronisation personnalisée pour optimiser la disponibilité et le niveau de performance dans Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.openlocfilehash: 2c989b352ef1b7800980c3a89b007c625198f822
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441812"
---
# <a name="implement-custom-synchronization-to-optimize-for-higher-availability-and-performance"></a>Implémenter une synchronisation personnalisée afin d’optimiser la disponibilité et le niveau de performance

Azure Cosmos DB propose [cinq niveaux de cohérence bien définis](consistency-levels.md) pour vous permettre de faire un compromis équilibré entre cohérence, niveau de performance et disponibilité. Une cohérence forte contribue à garantir une réplication synchrone et une conservation durable des données dans chaque région où un compte Azure Cosmos est disponible. Cette configuration offre le plus haut niveau de durabilité, mais ce résultat est obtenu au détriment du niveau de performance et de la disponibilité. Si vous souhaitez que votre application contrôle ou assouplisse la durabilité des données en fonction de ses besoins sans compromettre leur disponibilité, vous devez utiliser une *synchronisation personnalisée* au niveau de la couche Application pour atteindre le niveau de durabilité désiré.

L'image ci-dessous illustre visuellement le modèle d’une synchronisation personnalisée :

![Synchronisation personnalisée](./media/how-to-custom-synchronization/custom-synchronization.png)

Dans ce scénario, un conteneur Azure Cosmos est répliqué dans le monde entier, dans plusieurs régions et sur plusieurs continents. L’utilisation d’une cohérence forte pour toutes les régions dans ce scénario a un impact sur le niveau de performance. Pour garantir un plus haut niveau de durabilité des données sans compromettre la latence d’écriture, l’application peut utiliser deux clients qui partagent le même [jeton de session](how-to-manage-consistency.md#utilize-session-tokens).

Le premier client peut écrire des données dans la région locale (par exemple, USA Ouest). Le deuxième client (par exemple, USA Est) est un client de lecture utilisé pour garantir la synchronisation. En faisant passer le jeton de session de la réponse en écriture à la lecture suivante, la lecture garantit la synchronisation des écritures vers USA Est. Azure Cosmos DB veille à ce que les écritures soient vues par au moins une région. Elles résistent à une défaillance régionale en cas de panne au niveau de la région d’écriture d’origine. Dans ce scénario, chaque écriture est synchronisée sur USA Est, ce qui réduit la latence liée à l’utilisation d’une cohérence forte dans toutes les régions. Dans un scénario multimaître, où des écritures ont lieu dans chaque région, vous pouvez étendre ce modèle pour se synchroniser à plusieurs régions en parallèle.

## <a name="configure-the-clients"></a>Configurer les clients

L’exemple suivant montre une couche d’accès aux données qui instancie deux clients de synchronisation personnalisée :

### <a name="net-v2-sdk"></a>SDK .Net v2
```csharp
class MyDataAccessLayer
{
    private DocumentClient writeClient;
    private DocumentClient readClient;

    public async Task InitializeAsync(Uri accountEndpoint, string key)
    {
        ConnectionPolicy writeConnectionPolicy = new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp,
            UseMultipleWriteLocations = true
        };
        writeConnectionPolicy.SetCurrentLocation(LocationNames.WestUS);

        ConnectionPolicy readConnectionPolicy = new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp
        };
        readConnectionPolicy.SetCurrentLocation(LocationNames.EastUS);

        writeClient = new DocumentClient(accountEndpoint, key, writeConnectionPolicy);
        readClient = new DocumentClient(accountEndpoint, key, readConnectionPolicy, ConsistencyLevel.Session);

        await Task.WhenAll(new Task[]
        {
            writeClient.OpenAsync(),
            readClient.OpenAsync()
        });
    }
}
```

### <a name="net-v3-sdk"></a>SDK .Net v3
```csharp
class MyDataAccessLayer
{
    private CosmosClient writeClient;
    private CosmosClient readClient;

    public void InitializeAsync(string accountEndpoint, string key)
    {
        CosmosClientOptions writeConnectionOptions = new CosmosClientOptions();
        writeConnectionOptions.ConnectionMode = ConnectionMode.Direct;
        writeConnectionOptions.ApplicationRegion = "West US";

        CosmosClientOptions readConnectionOptions = new CosmosClientOptions();
        readConnectionOptions.ConnectionMode = ConnectionMode.Direct;
        readConnectionOptions.ApplicationRegion = "East US";


        writeClient = new CosmosClient(accountEndpoint, key, writeConnectionOptions);
        readClient = new CosmosClient(accountEndpoint, key, readConnectionOptions);
    }
}
```

## <a name="implement-custom-synchronization"></a>Implémenter une synchronisation personnalisée

Une fois les clients initialisés, l’application peut effectuer des écritures dans la région locale (USA Ouest) et forcer la synchronisation des écritures avec USA Est de la façon suivante.

### <a name="net-v2-sdk"></a>SDK .Net v2
```csharp
class MyDataAccessLayer
{
    public async Task CreateItem(string containerLink, Document document)
    {
        ResourceResponse<Document> response = await writeClient.CreateDocumentAsync(
            containerLink, document);

        await readClient.ReadDocumentAsync(response.Resource.SelfLink,
            new RequestOptions { SessionToken = response.SessionToken });
    }
}
```

### <a name="net-v3-sdk"></a>SDK .Net v3
```csharp
class MyDataAccessLayer
{
     public async Task CreateItem(string databaseId, string containerId, dynamic item)
     {
        ItemResponse<dynamic> response = await writeClient.GetContainer("containerId", "databaseId")
            .CreateItemAsync<dynamic>(
                item,
                new PartitionKey("test"));

        await readClient.GetContainer("containerId", "databaseId").ReadItemAsync<dynamic>(
            response.Resource.id,
            new PartitionKey("test"),
            new ItemRequestOptions { SessionToken = response.Headers.Session, ConsistencyLevel = ConsistencyLevel.Session });
    }
}
```

Vous pouvez étendre le modèle pour synchroniser plusieurs régions en parallèle.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la distribution et la cohérence globale dans Azure Cosmos DB, consultez les articles suivants :

* [Choisir le niveau de cohérence approprié dans Azure Cosmos DB](consistency-levels-choosing.md)
* [Compromis entre cohérence, disponibilité et niveau de performance dans Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Gérer la cohérence dans Azure Cosmos DB](how-to-manage-consistency.md)
* [Partitionnement et distribution des données dans Azure Cosmos DB](partition-data.md)
