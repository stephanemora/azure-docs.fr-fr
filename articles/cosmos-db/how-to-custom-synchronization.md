---
title: Guide pratique pour implémenter une synchronisation personnalisée afin d’optimiser la disponibilité et le niveau de performance dans Azure Cosmos DB
description: Découvrez comment implémenter une synchronisation personnalisée pour optimiser la disponibilité et le niveau de performance dans Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: rimman
ms.openlocfilehash: cd89a145f5746696cc8fc163eb46896081d85a90
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240960"
---
# <a name="implement-custom-synchronization-to-optimize-for-higher-availability-and-performance"></a>Implémenter une synchronisation personnalisée afin d’optimiser la disponibilité et le niveau de performance

Azure Cosmos DB propose [cinq niveaux de cohérence bien définis](consistency-levels.md) pour vous permettre de faire un compromis équilibré entre cohérence, niveau de performance et disponibilité. Une cohérence forte contribue à garantir une réplication synchrone et une conservation durable des données dans chaque région où un compte Azure Cosmos est disponible. Cette configuration offre le plus haut niveau de durabilité, mais ce résultat est obtenu au détriment du niveau de performance et de la disponibilité. Si vous souhaitez que votre application contrôle ou assouplisse la durabilité des données en fonction de ses besoins sans compromettre leur disponibilité, vous devez utiliser une *synchronisation personnalisée* au niveau de la couche Application pour atteindre le niveau de durabilité désiré.

L'image ci-dessous illustre visuellement le modèle d’une synchronisation personnalisée :

![Synchronisation personnalisée](./media/how-to-custom-synchronization/custom-synchronization.png)

Dans ce scénario, un conteneur Azure Cosmos est répliqué dans le monde entier, dans plusieurs régions et sur plusieurs continents. L’utilisation d’une cohérence forte pour toutes les régions dans ce scénario a un impact sur le niveau de performance. Pour garantir un plus haut niveau de durabilité des données sans compromettre la latence d’écriture, l’application peut utiliser deux clients qui partagent le même [jeton de session](how-to-manage-consistency.md#utilize-session-tokens).

Le premier client peut écrire des données dans la région locale (par exemple, USA Ouest). Le deuxième client (par exemple, USA Est) est un client de lecture utilisé pour garantir la synchronisation. En faisant passer le jeton de session de la réponse en écriture à la lecture suivante, la lecture garantit la synchronisation des écritures vers USA Est. Azure Cosmos DB veille à ce que les écritures soient vues par au moins une région. Elles résistent à une défaillance régionale en cas de panne au niveau de la région d’écriture d’origine. Dans ce scénario, chaque écriture est synchronisée sur USA Est, ce qui réduit la latence liée à l’utilisation d’une cohérence forte dans toutes les régions. Dans un scénario multimaître, où des écritures ont lieu dans chaque région, vous pouvez étendre ce modèle pour se synchroniser à plusieurs régions en parallèle.

## <a name="configure-the-clients"></a>Configurer les clients

L’exemple suivant montre une couche d’accès aux données qui instancie deux clients de synchronisation personnalisée :

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

## <a name="implement-custom-synchronization"></a>Implémenter une synchronisation personnalisée

Une fois les clients initialisés, l’application peut effectuer des écritures dans la région locale (USA Ouest) et forcer la synchronisation des écritures avec USA Est de la façon suivante.

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

Vous pouvez étendre le modèle pour synchroniser plusieurs régions en parallèle.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la distribution et la cohérence globale dans Azure Cosmos DB, consultez les articles suivants :

* [Choisir le niveau de cohérence approprié dans Azure Cosmos DB](consistency-levels-choosing.md)
* [Compromis entre cohérence, disponibilité et niveau de performance dans Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Gérer la cohérence dans Azure Cosmos DB](how-to-manage-consistency.md)
* [Partitionnement et distribution des données dans Azure Cosmos DB](partition-data.md)
