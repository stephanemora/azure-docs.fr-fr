---
title: Guide pratique pour implémenter une synchronisation personnalisée afin d’optimiser la disponibilité et le niveau de performance dans Azure Cosmos DB
description: Découvrez comment implémenter une synchronisation personnalisée pour optimiser la disponibilité et le niveau de performance dans Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/15/2019
ms.author: rimman
ms.openlocfilehash: d948798f161eb36578cb679b6d96409917424fd4
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678460"
---
# <a name="how-to-implement-custom-synchronization-to-optimize-for-higher-availability-and-performance"></a>Guide pratique pour implémenter une synchronisation personnalisée afin d’optimiser la disponibilité et le niveau de performance

Azure Cosmos DB propose [cinq niveaux de cohérence bien définis](consistency-levels.md) pour vous permettre de faire un compromis équilibré entre la cohérence, les performances et la disponibilité. Une cohérence forte garantit une réplication synchrone et une conservation durable des données dans chaque région où un compte Azure Cosmos est disponible. Bien qu’offrant le plus haut niveau de durabilité, cette configuration n’offre pas un niveau très élevé de performance et de disponibilité. Si une application souhaite contrôler/assouplir la durabilité des données en fonction de ses besoins sans compromettre leur disponibilité, elle peut utiliser une *synchronisation personnalisée* au niveau de la couche Application pour atteindre le niveau de durabilité recherché.

Le diagramme ci-dessous illustre visuellement le modèle d’une synchronisation personnalisée.

![Synchronisation personnalisée](./media/how-to-custom-synchronization/custom-synchronization.png)

Dans ce scénario, un conteneur Azure Cosmos est répliqué dans le monde entier, dans plusieurs régions et plusieurs continents. L’utilisation d’une cohérence forte pour toutes les régions de ce scénario a un impact sur les performances. Pour garantir un plus haut niveau de durabilité des données sans compromettre la latence d’écriture, l’application peut utiliser deux clients qui partagent le même [jeton de session](how-to-manage-consistency.md#utilize-session-tokens).

Le premier client peut écrire des données dans la région locale (par exemple, USA Ouest). Le deuxième client (par exemple, USA Est) est un client de lecture utilisé pour garantir la synchronisation. En faisant passer le jeton de session de la réponse en écriture à la lecture suivante, la lecture garantit la synchronisation des écritures vers USA Est. Azure Cosmos DB veille à ce que les écritures soient vues par au moins une région et résistent à une défaillance régionale, si la région d’écriture d’origine devait être touchée par une panne. Dans ce scénario, chaque écriture est synchronisée sur USA Est, ce qui réduit la latence liée à l’utilisation d’une cohérence forte dans toutes les régions. Dans un scénario multimaître, où des écritures ont lieu dans chaque région, ce modèle peut être étendu pour se synchroniser à plusieurs régions en parallèle.

## <a name="configure-the-clients"></a>Configurer les clients

L’exemple ci-dessous montre la couche d’accès aux données qui instancie deux clients dans ce but.

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

Ce modèle peut être étendu pour se synchroniser à plusieurs régions en parallèle.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la distribution et la cohérence globale dans Azure Cosmos DB, lisez les articles suivants :

* [Choix du niveau de cohérence approprié dans Azure Cosmos DB](consistency-levels-choosing.md)
* [Compromis entre cohérence, disponibilité et niveau de performance dans Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Guide pratique pour gérer la cohérence dans Azure Cosmos DB](how-to-manage-consistency.md)
* [Partitionnement et distribution des données dans Azure Cosmos DB](partition-data.md)
