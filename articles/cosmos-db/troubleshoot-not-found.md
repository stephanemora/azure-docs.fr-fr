---
title: Résoudre les problèmes liés à une exception Introuvable Azure Cosmos DB
description: Comment diagnostiquer et corriger une exception Introuvable
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: f391772672904a1e2bdfa0a741d9f85a6edeea8b
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87293743"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-not-found"></a>Diagnostiquer et résoudre les problèmes liés à une exception Introuvable Azure Cosmos DB
Le code d’état HTTP 404 indique que la ressource n’existe plus.

## <a name="expected-behavior"></a>Comportement attendu
Il existe de nombreux scénarios valides où une application attend un code 404 et gère correctement le scénario.

## <a name="not-found-was-returned-for-an-item-that-should-exist-or-does-exist"></a>Introuvable a été retourné pour un élément qui doit exister ou existe
Vous trouverez ci-dessous les raisons possibles pour qu’un code d’état 404 soit retourné si l’élément doit exister ou existe.

### <a name="1-race-condition"></a>1. Condition de concurrence critique
Il existe plusieurs instances de client SDK et la lecture a eu lieu avant l’écriture.

#### <a name="solution"></a>Solution :
1. La cohérence de compte par défaut pour Cosmos DB est la cohérence de session. Quand un élément est créé ou mis à jour, la réponse retourne un jeton de session qui peut être transmis entre les instances de SDK pour garantir que la demande de lecture lit à partir d’un réplica avec cette modification.
2. Affectez au [niveau de cohérence](consistency-levels-choosing.md) un [niveau plus fort](consistency-levels-tradeoffs.md)

### <a name="2-invalid-partition-key-and-id-combination"></a>2. Combinaison de la clé de partition et de l’ID non valide
La combinaison de la clé de partition et de l’ID n’est pas valide.

#### <a name="solution"></a>Solution :
Corrigez la logique d’application qui provoque la combinaison incorrecte. 

### <a name="3-invalid-character-in-item-id"></a>3. Caractère non valide dans l’ID d’élément
Un élément est inséré dans Cosmos DB avec un [caractère non valide](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet#remarks) dans l’ID d’élément.

#### <a name="solution"></a>Solution :
Il est recommandé aux utilisateurs de remplacer l’ID par une valeur différente qui ne contient pas les caractères spéciaux. Si vous ne pouvez pas modifier l’ID, vous pouvez l’encoder au format Base64 pour placer les caractères spéciaux dans une séquence d’échappement.

Pour les éléments déjà insérés dans le conteneur, l’ID peut être remplacé avec des valeurs RID plutôt que des références basées sur un nom.
```c#
// Get a container reference that use RID values
ContainerProperties containerProperties = await this.Container.ReadContainerAsync();
string[] selfLinkSegments = containerProperties.SelfLink.Split('/');
string databaseRid = selfLinkSegments[1];
string containerRid = selfLinkSegments[3];
Container containerByRid = this.cosmosClient.GetContainer(databaseRid, containerRid);

// List of invalid characters are listed here.
//https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet#remarks
FeedIterator<JObject> invalidItemsIterator = this.Container.GetItemQueryIterator<JObject>(
    @"select * from t where CONTAINS(t.id, ""/"") or CONTAINS(t.id, ""#"") or CONTAINS(t.id, ""?"") or CONTAINS(t.id, ""\\"") ");
while (invalidItemsIterator.HasMoreResults)
{
    foreach (JObject itemWithInvalidId in await invalidItemsIterator.ReadNextAsync())
    {
        // It recommend to chose a new ID that does not contain special characters, but
        // if that is not possible then it can be Base64 encoded to escape the special characters
        byte[] plainTextBytes = Encoding.UTF8.GetBytes(itemWithInvalidId["id"].ToString());
        itemWithInvalidId["id"] = Convert.ToBase64String(plainTextBytes);

        // Update the item with the new ID value using the rid based container reference
        JObject item = await containerByRid.ReplaceItemAsync<JObject>(
            item: itemWithInvalidId,
            ID: itemWithInvalidId["_rid"].ToString(),
            partitionKey: new Cosmos.PartitionKey(itemWithInvalidId["status"].ToString()));

        // Validate the new ID can be read using the original name based contianer reference
        await this.Container.ReadItemAsync<ToDoActivity>(
            item["id"].ToString(),
            new Cosmos.PartitionKey(item["status"].ToString())); ;
    }
}
```

### <a name="4-time-to-live-ttl-purge"></a>4. Suppression définitive avec la durée de vie
La propriété [Durée de vie](https://docs.microsoft.com/azure/cosmos-db/time-to-live) a été définie pour l’élément. L’élément a été supprimé définitivement, car la durée de vie a expiré.

#### <a name="solution"></a>Solution :
Modifiez la durée de vie pour empêcher l’élément d’être supprimé définitivement.

### <a name="5-lazy-indexing"></a>5. Indexation différée
L’[indexation différée](index-policy.md#indexing-mode) n’est pas à jour.

#### <a name="solution"></a>Solution :
Attendez que l’indexation soit à jour ou modifiez la stratégie d’indexation

### <a name="6-parent-resource-deleted"></a>6. Ressource parente supprimée
La base de données et/ou le conteneur dans lequel se trouve l’élément a été supprimé.

#### <a name="solution"></a>Solution :
1. [Restaurez](https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore#backup-retention-period) la ressource parente ou recréez les ressources.
2. Créez une ressource pour remplacer la ressource supprimée

## <a name="next-steps"></a>Étapes suivantes
* [Diagnostiquer et résoudre](troubleshoot-dot-net-sdk.md) les problèmes lors de l’utilisation du SDK .NET Azure Cosmos DB
* Découvrez les recommandations relatives aux performances pour [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) et [.NET V2](performance-tips.md)